<template>
  <div class="topic-box">
    <div class="topic-box-title">我的话题</div>
    <div class="topic-list" v-if="topicList.length">
      <div class="topic-item" v-for="item in topicList" :key="item.date">
        <div class="topic-item-time">{{ item.date }}</div>
        <div
          class="topic-item-content"
          v-for="(topic, index) in item.topics"
          :key="topic.id"
        >
          <div
            class="topic-item-title flex"
            style="justify-content: space-between"
          >
            <div class="text" @click.stop="handleToDetail(topic)">
              {{ topic.title }}
            </div>
            <!-- <div :class="['topic-item-type',topic.is_open == 1? 'active':'']">{{ topic.is_open == 1 ? '公开' :'私密'}}</div> -->

            <div>
              <el-switch
                v-model="topic.is_open"
                @change="handleTopicOpen(topic)"
                :active-value="1"
                :inactive-value="0"
                inline-prompt
                active-text="公开"
                inactive-text="私密"
              />
            </div>
          </div>
          <div class="topic-item-text">
            {{ topic.messages.length ? topic.messages[0].content : null }}
          </div>
          <div class="topic-item-base">
            <div class="flex" style="align-items: center">
              <div class="base-box flex">
                <div class="flex">
                  <img
                    :src="
                      !topic.favorite_active
                        ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/AnhT4Inxw4lkcEF6spx5WFJImQdmS3VS6LUgbEsL.svg'
                        : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/hFiJogbyXyfqh3S2cFJrIvOd0uiqyRHln32LahWJ.svg'
                    "
                    alt=""
                  />
                  <div class="text">{{ topic.favorite.length }}</div>
                </div>

                <div class="flex" style="margin-left: 18px">
                  <img
                    :src="
                      !topic.collect_active
                        ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/zzRTqjWwocSpW41IHUWuk1hpxcmyZnYnjl78vf45.svg'
                        : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/YRXLLtRFV2Dqog9MWzqRTOKewC35IDYzd1IyAHVD.svg'
                    "
                    alt=""
                  />
                  <div class="text">{{ topic.collect.length }}</div>
                </div>
                <div
                  class="flex"
                  style="margin-left: 18px; cursor: pointer"
                  @click.stop="handleTopicAction(2, topic)"
                ></div>
              </div>
              <el-icon class="delete-icon" @click.stop="handleDelete(topic)">
                <Delete />
              </el-icon>
            </div>
            <div class="item-base-label flex">
              <div v-for="tag in topic.tags">{{ tag.name }}</div>
            </div>
          </div>
          <div
            v-if="index != item.topics.length - 1"
            class="topic-item-line"
          ></div>
        </div>
      </div>
    </div>
    <div class="topic-list none" v-else>
      <div>
        <img
          src="https://assets.jiker.com/_for_common_project/2024/1220/admin/xXh2H8zusFHR5qiH6HvTiROphfYb9jknCgnIBuj2.png"
        />
        <div>暂无内容</div>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ElMessage, ElMessageBox } from "element-plus";

import mixAxios from "mix-axios";
import { reactive, ref } from "vue";
const topicList = ref([]);
const userInfo = ref({});
const value3 = ref({});
const getData = () => {
  mixAxios.get("/app-api/community/topic/my").then((res) => {
    topicList.value = res.map((item) => {
      item.topics.forEach((topic) => {
        topic.collect_active = Boolean(
          topic.collect.filter((j) => j.user_id == userInfo.value.id).length
        );
        topic.favorite_active = Boolean(
          topic.favorite.filter((j) => j.user_id == userInfo.value.id).length
        );
      });
      return item;
    });
    console.log(topicList.value);
  });
};
const handleTopicAction = (type, data) => {
  const params = {
    topic_id: data.id,
    type,
  };
  mixAxios.post("/app-api/community/topic/actions", params).then(() => {
    ElMessage.success("操作成功！");
    getData();
  });
};
const getUser = () => {
  mixAxios.get("/api/user/info").then((res) => {
    userInfo.value = res;
    getData();
  });
};
const handleToDetail = (e) => {
  console.log(e);
  // const url =`/app/community/pure-page/nZ-lFmwb?id=${e.id}&from=my`;
  // window.location.href = url;
  const url = `/app/community/pure-page/nZ-lFmwb?id=${e.id}&referer=${window.location.href}`;
  window.location.href = url;
};
const handleDelete = (topic) => {
  // /app-api/community/topic/:id/switch-online
  ElMessageBox.confirm(`确定要删除此话题吗?`, "提示", {
    confirmButtonText: "确 定",
    cancelButtonText: "取 消",
    type: "warning",
  }).then(() => {
    mixAxios
      .put(`/app-api/community/topic/${topic.id}/switch-online`, {
        is_online: Boolean(topic.deleted_at),
      })
      .then(() => {
        ElMessage.success("操作成功！");
        getData();
      });
  });
};

const handleTopicOpen = (topic) => {
  console.log(topic);
  mixAxios
    .put(`/app-api/community/topic/${topic.id}/open`, {
      is_open: topic.is_open,
    })
    .then(() => {
      ElMessage.success(
        `${topic.is_open == 1 ? "话题公开成功" : "话题私密成功"}`
      );
    });
};

getUser();
</script>
<style scoped>
.delete-icon {
  color: rgba(0, 0, 0, 0.45);
  font-size: 16px;
  cursor: pointer;
}

.delete-icon:hover {
  color: red;
}

.topic-box {
  padding-top: 18px;

  .topic-box-title {
    font-family: PingFangSC, PingFang SC;
    font-weight: 500;
    font-size: 20px;
    color: rgba(0, 0, 0, 0.85);
    line-height: 28px;
    margin-bottom: 20px;
  }

  .topic-list {
    &.none {
      height: 500px;
      display: flex;
      align-items: center;
      justify-content: center;
      background: #fff;

      img {
        width: 104px;
        height: 104px;
      }

      text-align: center;

      div {
        /* margin-top:4px; */
        font-family: PingFangSC, PingFang SC;
        font-weight: 400;
        font-size: 14px;
        color: rgba(0, 0, 0, 0.45);
        /* line-height: 24px; */
      }
    }

    .topic-item {
      background-color: #fff;
      width: 100%;
      /* padding: 0 24px; */
      margin-bottom: 16px;

      .topic-item-time {
        height: 56px;
        line-height: 56px;
        width: 100%;
        font-family: PingFangSC, PingFang SC;
        font-weight: 400;
        font-size: 16px;
        padding: 0 24px;
        color: rgba(0, 0, 0, 0.45);
        border-bottom: 1px solid rgba(0, 0, 0, 0.06);
      }

      .topic-item-content {
        /* border-top: 1px solid rgba(0, 0, 0, 0.06); */
        height: 138px;
        padding: 16px 24px 0;

        &:hover {
          background: #fafafa;
        }

        .topic-item-title {
          .text {
            font-family: PingFangSC, PingFang SC;
            font-weight: 500;
            font-size: 16px;
            width: 400px;
            color: rgba(0, 0, 0, 0.85);
            line-height: 24px;
            cursor: pointer;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
          }

          .topic-item-type {
            text-align: center;
            width: 36px;
            height: 20px;
            line-height: 20px;
            background: #858c97;
            border-radius: 10px;
            font-family: PingFangSC, PingFang SC;
            font-weight: 400;
            font-size: 12px;
            color: #ffffff;

            &.active {
              background: #36b355;
            }
          }
        }

        .topic-item-text {
          margin: 8px 0 0;
          font-family: PingFangSC, PingFang SC;
          font-weight: 400;
          font-size: 14px;
          color: rgba(0, 0, 0, 0.45);
          overflow: hidden;
          line-height: 22px;
          height: 44px;
          text-overflow: ellipsis;
          display: -webkit-box;
          -webkit-line-clamp: 2;
          -webkit-box-orient: vertical;
        }

        .topic-item-base {
          height: 22px;
          display: flex;
          align-items: center;
          justify-content: space-between;

          .base-box {
            align-items: center;

            .text {
              font-family: PingFangSC, PingFang SC;
              font-weight: 400;
              font-size: 14px;
              color: rgba(0, 0, 0, 0.45);
            }

            .line {
              width: 1px;
              height: 12px;
              background: #d8d8d8;
              margin: 0 12px;
            }

            img {
              margin-right: 4px;
            }
          }

          .item-base-label {
            div {
              background: rgba(0, 0, 0, 0.04);
              padding: 3px 6px;
              text-align: center;
              font-family: PingFangSC, PingFang SC;
              font-weight: 400;
              font-size: 12px;
              color: rgba(0, 0, 0, 0.45);
              margin-left: 8px;
            }
          }
        }
      }
    }
  }
}

.topic-item-line {
  margin-top: 16px;
  width: 100%;
  height: 1px;
  background: rgba(0, 0, 0, 0.1);
}
</style>
<style>
.page-body-container {
  background: #f5f5f5 !important;
}
</style>
