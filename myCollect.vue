<template>
  <div class="collect-box">
    <div class="collect-box-title">我的收藏</div>
    <div class="topic-list" v-if="topicList.length">
      <div
        class="collect-box-content"
        @click.stop="handleToDetail(item)"
        v-for="item in topicList"
        :key="item.id"
      >
        <div class="collect-content-item">
          <div class="topic-item-title">{{ item.title }}</div>
          <div class="topic-item-text">
            {{ item.messages.length ? item.messages[0].content : null }}
          </div>
          <div class="topic-item-base">
            <div class="base-box flex">
              <div class="text">{{ item.created_at }}</div>

              <div class="line"></div>

              <div class="text">{{ item.user_name }}</div>
              <div class="line"></div>
              <div
                class="flex"
                @click.stop="handleTopicAction(1, item)"
                style="cursor: pointer"
              >
                <img
                  :src="
                    !item.favorite_active
                      ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/AnhT4Inxw4lkcEF6spx5WFJImQdmS3VS6LUgbEsL.svg'
                      : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/hFiJogbyXyfqh3S2cFJrIvOd0uiqyRHln32LahWJ.svg'
                  "
                  alt=""
                />
                <div class="text">{{ item.favorite.length || "喜欢" }}</div>
              </div>
              <div
                class="flex"
                style="margin-left: 18px; cursor: pointer"
                @click.stop="handleTopicAction(2, item)"
              >
                <img
                  :src="
                    !item.collect_active
                      ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/zzRTqjWwocSpW41IHUWuk1hpxcmyZnYnjl78vf45.svg'
                      : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/YRXLLtRFV2Dqog9MWzqRTOKewC35IDYzd1IyAHVD.svg'
                  "
                  alt=""
                />
                <div class="text">{{ item.collect.length || "收藏" }}</div>
              </div>
            </div>
            <div class="item-base-label flex">
              <div v-for="tag in item.tags">{{ tag.name }}</div>
            </div>
          </div>
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
import mixAxios from "mix-axios";
import { ElMessage, ElMessageBox } from "element-plus";

import { reactive, ref } from "vue";
const topicList = ref([]);
const userInfo = ref({});
const getData = () => {
  mixAxios.get("/app-api/community/topic/my/collect").then((res) => {
    topicList.value = res.map((item) => {
      item.collect_active = Boolean(
        item.collect.filter((j) => j.user_id == userInfo.value.id).length
      );
      item.favorite_active = Boolean(
        item.favorite.filter((j) => j.user_id == userInfo.value.id).length
      );
      return item;
    });
    console.log(topicList.value);
  });
};
const handleToDetail = (e) => {
  console.log(document.URL);
  const url = `/app/community/pure-page/nZ-lFmwb?id=${e.id}&referer=${window.location.href}`;
  window.location.href = url;
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
getUser();
</script>
<style scoped>
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
}

.collect-box {
  width: 910px;
  background: #f5f5f5 !important;

  padding-top: 18px;

  .collect-box-title {
    font-family: PingFangSC, PingFang SC;
    font-weight: 500;
    font-size: 20px;
    color: rgba(0, 0, 0, 0.85);
    line-height: 28px;
    margin-bottom: 20px;
  }

  .collect-box-content {
    /* border-top: 1px solid rgba(0, 0, 0, 0.06); */
    height: 138px;
    padding: 16px 24px;
    cursor: pointer;

    background-color: #fff;
    margin-bottom: 16px;

    .topic-item-title {
      width: 400px;
      font-family: PingFangSC, PingFang SC;
      font-weight: 500;
      font-size: 16px;
      color: rgba(0, 0, 0, 0.85);
      line-height: 24px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;

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
      margin: 8px 0;
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
</style>
<style>
.page-body-container {
  background: #f5f5f5 !important;
}
</style>
