<template>
  <div class="section-container" ref="scrollContainer">
    <div class="home-box">
      <div class="home-content">
        <!-- <div class="home-content-banner"></div> -->
        <div class="home-content-img" @click="handleToHelp">
          <div class="title">知识社区</div>
          <div class="tag">共建 · 共创 · 共享</div>
          <div class="text">遵守社区守则，共同维护健康网络环境</div>
          <div class="btn">了解更多</div>
        </div>
        <div class="home-topic-box">
          <div class="topic-top flex">
            <el-select
              v-model="currentTag"
              filterable
              @change="getTopic"
              placeholder="Select"
              style="width: 100px"
            >
              <el-option
                v-for="item in tagList"
                :key="item.id"
                :label="item.name"
                :value="item.id"
              />
            </el-select>
            <div class="flex">
              <div
                @click="(selectSort = 1), getTopic()"
                :class="['topic-sort', selectSort == 1 ? 'active' : '']"
                style="margin-right: 16px"
              >
                热门
              </div>
              <div
                @click="(selectSort = 2), getTopic()"
                :class="['topic-sort', selectSort == 2 ? 'active' : '']"
              >
                最新
              </div>
            </div>
          </div>
          <div class="topic-list" v-if="topicList.length">
            <div
              class="topic-item"
              v-for="(item, index) in topicList"
              @click.stop="handleToDetail(item)"
              :key="item.id"
            >
              <div class="topic-item-title">
                {{ item.title }}
              </div>
              <div class="topic-item-text">
                {{ item.messages.length ? item.messages[0].content : null }}
              </div>
              <div class="topic-item-base">
                <div class="base-box flex">
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
              <div
                :style="`${index == topicList.length - 1 ? ' opacity: 0' : ''}`"
                class="topic-item-line"
              ></div>
            </div>
          </div>
          <div v-else class="topic-list none">
            <div>
              <img
                src="https://assets.jiker.com/_for_common_project/2024/1220/admin/xXh2H8zusFHR5qiH6HvTiROphfYb9jknCgnIBuj2.png"
              />
              <div>暂无内容</div>
            </div>
          </div>
        </div>
      </div>
      <div class="home-right">
        <div class="home-right-btn" @click="drawer = true">
          <img :src="imgList.star" /> 我要提问
        </div>
        <div class="home-right-user flex">
          <img
            class="user-img"
            :src="
              userInfo.avatar_url ||
              'https://assets.jiker.com/_for_common_project/2024/1212/admin/IlIPFyfVqw4sYSqaiWYLQOYxQD3vF21CcxrmwifC.png'
            "
          />
          <div>
            <div class="user-name">{{ userInfo.name }}</div>
            <div class="user-job">
              {{ userInfo.created_at?.slice(0, 10) }} 加入
            </div>
          </div>
        </div>
        <div class="home-right-statistics">
          <div style="text-align: center">
            <div class="number">
              {{ userData.topic_count > 999 ? "999+" : userData.topic_count }}
            </div>
            <div class="text">提问</div>
          </div>
          <div style="text-align: center">
            <div class="number">
              {{
                userData.message_count > 999 ? "999+" : userData.message_count
              }}
            </div>
            <div class="text">问答</div>
          </div>
          <div style="text-align: center">
            <div class="number">
              {{
                userData.collect_count > 999 ? "999+" : userData.collect_count
              }}
            </div>
            <div class="text">收藏</div>
          </div>
          <div style="text-align: center">
            <div class="number">
              {{ userData.fav_count > 999 ? "999+" : userData.fav_count }}
            </div>
            <div class="text">喜欢</div>
          </div>
        </div>
        <div class="home-right-item">
          <div class="title">推荐话题</div>
          <div
            class="popular-item"
            @click="handleToDetail(item)"
            v-for="item in topicPopularList"
            :key="item.id"
          >
            <div>
              {{ item.title }}
            </div>
          </div>
        </div>
        <div class="home-right-item">
          <div class="title">达人榜</div>
          <div class="expert-item flex" v-for="item in rankList" :key="item.id">
            <!-- <div ></div> -->
            <img
              class="expert-item-img"
              :src="
                item.avatar_url ||
                'https://assets.jiker.com/_for_common_project/2024/1212/admin/IlIPFyfVqw4sYSqaiWYLQOYxQD3vF21CcxrmwifC.png'
              "
            />
            <div>
              <div class="expert-item-name">{{ item.name }}</div>
              <div class="expert-item-text">
                {{ item.count }} 问题 · {{ item.fav_count }} 喜欢
              </div>
            </div>
          </div>
        </div>
        <div class="home-right-item" style="padding-right: 0">
          <div class="title">热门标签</div>
          <div class="label-list">
            <div
              class="label-item"
              v-for="item in tagPopularList"
              :key="item.id"
            >
              {{ item.name }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <el-drawer
    v-model="drawer"
    @close="handleDrawerClose"
    size="1180px"
    :with-header="false"
  >
    <div class="drawer-box" style="height: 100vh">
      <div class="drawer-header-box">
        <div class="flex">
          <div class="header-back" @click="drawer = false">
            <el-icon>
              <ArrowLeft />
            </el-icon>
            返回
          </div>
          <div class="header-line"></div>
          <div class="header-title">{{ topicInfo.title || "未命名话题" }}</div>
        </div>

        <el-button
          type="success"
          @click="dialogVisible = true"
          :disabled="!topicId"
          >话题发布</el-button
        >
      </div>
      <div class="drawer-body-box" ref="drawerContent">
        <div class="section-content">
          <div
            :class="['message-item', (index + 1) % 2 == 0 ? 'right' : '']"
            v-for="(item, index) in messageList"
            :key="index"
            :style="index == 0 ? 'margin-bottom:40px' : 'margin-bottom:14px;'"
          >
            <img
              class="message-item-img"
              :src="
                (index + 1) % 2 !== 0
                  ? 'https://assets.jiker.com/_for_common_project/2024/1212/admin/ixJqTYIozVGK4DKgf33v3nK09TZzoBAp2f15dBS0.svg'
                  : userInfo.avatar_url
                  ? userInfo.avatar_url
                  : 'https://assets.jiker.com/_for_common_project/2024/1212/admin/IlIPFyfVqw4sYSqaiWYLQOYxQD3vF21CcxrmwifC.png'
              "
            />
            <div>
              <div
                :class="[
                  'message-item-content',
                  (index + 1) % 2 == 0 ? 'right' : '',
                ]"
                :style="
                  (index + 1) % 2 == 0
                    ? 'margin-right: 12px'
                    : 'margin-left: 12px'
                "
              >
                <div class="text">
                  <Markdown v-model="item.content" :toolbarsFlag="false" />
                  <!-- {{ item.content }} -->
                  <!-- <component :is="Markdown" v-model="item.content" :toolbarsFlag="false"></component> -->
                </div>
              </div>
              <div
                class="footer-tips"
                v-if="(index + 1) % 2 !== 0"
                style="text-align: left; padding-left: 14px; margin-top: 8px"
              >
                内容由 AI 大模型生成，请仔细甄别
              </div>
            </div>
          </div>
          <div class="loading-box" v-if="btnLoading">
            <img
              src="https://assets.jiker.com/_for_common_project/2025/0109/admin/iGHt0dZGwCb6isZWjaOkFwdUZtmlZoc6FiZRkbs3.svg"
            />
            <div style="display: flex; align-items: flex-end">
              <div class="loading-box-title">{{ btnLoadingText }}</div>
              <div class="loading">
                <div></div>
                <div></div>
                <div></div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="drawer-footer-box">
        <div class="section-footer">
          <div class="footer-tips">内容由 AI 大模型生成，请仔细甄别</div>

          <div :class="['footer-input-box', message ? 'active' : '']">
            <div class="footer-input-container">
              <el-input
                class="footer-input"
                v-model="message"
                placeholder="请输入内容"
                :autosize="{ minRows: 1, maxRows: 3 }"
                type="textarea"
              ></el-input>
            </div>
            <div class="input-btn-box">
              <div></div>
              <div
                :class="['input-btn', message && !btnLoading ? 'active' : '']"
                @click="handleSend"
              >
                <img
                  v-show="!message"
                  src="https://assets.jiker.com/_for_common_project/2024/0920/admin/VzHJBpFvtZbsGKuwqKj8J6dx14I1F1nWT4pcxNGf.svg"
                />
                <img
                  v-show="message"
                  src="https://assets.jiker.com/_for_common_project/2024/0920/admin/KWDF0jvvqHbFvurohb76XAKc6Y9fwtoaQXGgYl3q.svg"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </el-drawer>
  <el-dialog
    v-model="dialogVisible"
    title="话题发布"
    width="406px"
    top="25vh"
    class="dialog-box"
    append-to-body
  >
    <div>
      <el-form :model="formData" label-width="auto" style="max-width: 600px">
        <el-form-item label="标题：" prop="title">
          <el-input
            placeholder="请输入"
            class="custom-textarea"
            maxlength="30"
            type="textarea"
            :autosize="{ minRows: 4, maxRows: 4 }"
            v-model="formData.title"
          />
        </el-form-item>
        <el-form-item label="分类：" prop="tags">
          <el-select
            v-if="!formTagStatus"
            v-model="formData.tags"
            @change="handleShowAddTag"
            multiple
            placeholder="请选择"
            value-key="id"
          >
            <el-option
              v-for="item in tagDrawerList"
              :key="item.id"
              :label="item.name"
              :value="item"
            />
          </el-select>
          <div class="flex" v-else>
            <el-input
              placeholder="请输入"
              style="width: 200px"
              v-model="newTag"
            />
            <div class="add-tag-btn" @click="handleAddTag">确定</div>
            <div class="add-tag-btn right" @click="handleCancelTag">取消</div>
          </div>
        </el-form-item>
        <el-form-item label="状态：" style="margin-bottom: 0">
          <el-select v-model="formData.is_open" placeholder="请选择">
            <el-option label="公开，所有人可见" :value="1" />
            <el-option label="私密，仅自己可见" :value="0" />
          </el-select>
          <!-- <span style="margin-right: 6px">
            {{ formData.is_open == 1 ? "公开" : "私密" }}
          </span>
          <el-switch v-model="formData.is_open" style="
              --el-switch-on-color: #00c700;
              --el-switch-off-color: #cccccc;
            " /> -->
        </el-form-item>
      </el-form>
    </div>
    <template #footer>
      <div class="dialog-footer">
        <el-button
          type="success"
          style="display: inline-block"
          @click="handleSubmit"
          :disabled="!topicId"
        >
          确 定</el-button
        >
      </div>
    </template>
  </el-dialog>
</template>

<script setup lang="ts">
import mixAxios from "mix-axios";
import axios from "axios";
import { ElMessage, ElMessageBox } from "element-plus";
import { reactive, ref, onMounted, watch } from "vue";
import { fetchEventSource } from "fetch-event-source";

const scrollContainer = ref(null);
const currentTag = ref(0);
const topicList = ref([]);
const tagList = ref([]);
const tagDrawerList = ref([]);
const rankList = ref([]);
const topicPopularList = ref([]);
const tagPopularList = ref([]);
let page = ref(1);
const selectSort = ref(1);
const userInfo = ref({});
const stopLoad = ref(false);
const drawer = ref(false);
const initLoad = ref(false);
const userData = ref({});

const messageList = ref([
  {
    content: `为了您的问题被更好的解决，建议您：<br />1 - 尽量完整的描述问题;<br />2 - 提供相关背景信息；<br />3 - 避免使用模糊的词汇；<br />4 - 明确您的期望结果。
`,
    role: "gpt",
  },
]);

const message = ref("");
const newTag = ref("");
const topicId = ref<any>(null);
const dialogVisible = ref(false);
const btnLoading = ref(false);
const btnLoadingText = ref("问题已接收");
const topicInfo = ref({});

const formData = reactive({
  title: "",
  tags: [] as any,
  is_open: 1,
});
const intervalId = ref();
const formTagStatus = ref(false);
const chatLoading = ref(false);
const drawerContent = ref(null);

watch(
  () => btnLoading.value,
  (newVal, oldVal) => {
    if (newVal) {
      intervalId.value = setInterval(handleUpdateLoading, 2000);
    }
  },
  { deep: true }
);

onMounted(() => {});

const handleUpdateLoading = () => {
  let list = ["问题已接收", "理解问题", "正在思考中"];
  let index = list.findIndex((item) => btnLoadingText.value == item);
  if (index == 2) {
    clearInterval(intervalId.value);
  } else {
    btnLoadingText.value = list[index + 1];
  }
};
const handleDrawerClose = () => {
  getData();
};
const getData = () => {
  Promise.all([
    mixAxios.get("/api/user/info"),
    mixAxios.get("/app-api/community/tag/popular"),
    mixAxios.get("/app-api/community/tags"),
    mixAxios.get("/app-api/community/talent-rank"),
    mixAxios.get("/app-api/community/topic/popular"),
    mixAxios.get("/app-api/community/my/data"),
  ]).then((res) => {
    userInfo.value = res[0];
    tagList.value = JSON.parse(JSON.stringify(res[2]));
    tagPopularList.value = res[1];
    tagList.value.unshift({
      id: 0,
      name: "全部",
    });
    rankList.value = res[3];
    topicPopularList.value = res[4];
    userData.value = res[5];

    tagDrawerList.value = JSON.parse(JSON.stringify(res[2]));
    tagDrawerList.value.push({ id: 0, name: "添加选项" });
    getTopic();
  });
};

const handleToDetail = (e) => {
  const url = `/app/community/pure-page/nZ-lFmwb?id=${e.id}&referer=${window.location.href}`;
  window.location.href = url;
};

const getTopic = () => {
  const params = {
    type: selectSort.value,
    tag_id: currentTag.value,
    page: page.value,
  };
  mixAxios.get("/app-api/community/home/topic", params).then((res) => {
    stopLoad.value = Boolean(res.data.length);
    topicList.value = [
      ...res.data.map((item) => {
        item.collect_active = Boolean(
          item.collect.filter((j) => j.user_id == userInfo.value.id).length
        );
        item.favorite_active = Boolean(
          item.favorite.filter((j) => j.user_id == userInfo.value.id).length
        );
        return item;
      }),
    ];
  });
};

const handleTopicAction = (type, data) => {
  const params = {
    topic_id: data.id,
    type,
  };
  mixAxios.post("/app-api/community/topic/actions", params).then(() => {
    ElMessage.success("操作成功！");
    if (type == 1) {
      Promise.all([
        mixAxios.get("/app-api/community/my/data"),
        mixAxios.get("/app-api/community/talent-rank"),
      ]).then((res) => {
        userData.value = res[0];
        rankList.value = res[1];
      });
    }

    getTopic();
  });
};

const getTopicTitle = async (content) => {
  const url = `https://gpt-service.jiker.cc/api/v2/chat`;
  const data = {
    messages: [
      {
        role: "user",
        content: `${content}请根据前面这段话总结50字以内的标题,除了标题什么也不要,标题不需要“”`,
      },
    ],
    stream: false,
  };
  const title = await axios({
    method: "post",
    url,
    data,
    headers: {
      Token: "1df65034830b214078fe",
    },
  });
  return title.data.choices[0].message.content;
};

const handleSend = async () => {
  if (!message.value.trim()) {
    return ElMessage.warning("请输入内容");
  }
  if (btnLoading.value) {
    return;
  }
  btnLoading.value = true;
  if (!topicId.value) {
    let title = await getTopicTitle(message.value);
    let data = await mixAxios.post("/app-api/community/topic/default", {
      title: title,
    });

    topicId.value = data.id;
    topicInfo.value = data;
    formData.title = data.title;
  }
  const abortController = new AbortController();
  const url = `https://gpt-service.jiker.cc/api/v2/chat`;
  let messageDate = "";

  const params = {
    role: "user",
    content: message.value,
    topic_id: topicId.value,
    pre_message_id: messageList.value[messageList.value.length - 1].id,
  };
  mixAxios.post("/app-api/community/topic/message", params).then((res) => {
    messageList.value.push({
      content: message.value,
      role: "user",
      id: res.id,
    });
    setTimeout(function firstFunction() {
      drawerContent.value.scrollTo({
        top: drawerContent.value.scrollHeight + 50,
        behavior: "smooth",
      });
    }, 1000);
    // btnLoading.value = true;
    message.value = "";
    const data = {
      stream: true,
      messages: messageList.value.filter((item) =>
        ["user", "assistant"].includes(item.role)
      ),
    };
    fetchEventSource(url, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Token: "1df65034830b214078fe",
      },
      body: JSON.stringify(data),
      signal: abortController.signal,
      onopen() {
        messageList.value.push({ content: "", role: "assistant", id: "" });
        message.value = "";
      },
      onmessage(event) {
        const data = JSON.parse(event.data);
        if (data.choices[0].finish_reason == "stop") {
          abortController.abort();
          setTimeout(function firstFunction() {
            drawerContent.value.scrollTo({
              top: drawerContent.value.scrollHeight + 50,
              behavior: "smooth",
            });
          }, 1000);
          mixAxios
            .post("/app-api/community/topic/message", {
              content: messageDate,
              role: "assistant",
              topic_id: topicId.value,
              pre_message_id: res.id,
            })
            .then((res) => {
              messageList.value[messageList.value.length - 1].id = res.id;
            });
          btnLoading.value = false;
          btnLoadingText.value = "问题已接收";
        } else {
          messageDate += data.choices[0].delta?.content;
          messageList.value[messageList.value.length - 1].content = messageDate;
        }
      },
      onerror(err) {
        btnLoading.value = false;
        abortController.abort();
        throw err;
      },
      onclose() {
        btnLoading.value = false;
        abortController.abort();
      },
      openWhenHidden: true,
    });
  });
};

const handleShowAddTag = (e) => {
  if (e[e.length - 1].name == "添加选项") {
    formTagStatus.value = true;
  }
};

const handleAddTag = () => {
  if (!newTag.value) {
    return ElMessage.warning("请输入标签名称");
  }

  mixAxios
    .post("/app-api/community/add/tag", { name: newTag.value })
    .then((res) => {
      const index = formData.tags.findIndex((item) => item.id == 0);
      formData.tags.splice(index, 1, res);
      console.log(formData.tags);
      mixAxios.get("/app-api/community/tags").then((data) => {
        console.log(data);
        tagDrawerList.value = JSON.parse(JSON.stringify(data));
        tagDrawerList.value.push({ id: 0, name: "添加选项" });
        formTagStatus.value = false;
        newTag.value = "";
      });
    });
};

const handleCancelTag = () => {
  const index = formData.tags.findIndex((item) => item.id == 0);
  formData.tags.splice(index, 1);
  formTagStatus.value = false;
  newTag.value = "";
};

const handleToHelp = () => {
  window.location.href = "/app/community/my/help/us";
};

const handleSubmit = () => {
  console.log(formData.tags.length);
  if (formData.tags.length == 0) {
    return ElMessage.warning("请选择话题分类");
  }
  if (!formData.title) {
    return ElMessage.warning("请输入话题标题");
  }
  const params = {
    ...formData,
    topic_id: topicId.value,
  };
  mixAxios.put("/app-api/community/topic", params).then(() => {
    dialogVisible.value = false;
    drawer.value = false;
    ElMessage.success("话题保存成功！");
  });
};

getData();

const imgList = {
  star: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAYAAABzenr0AAAAAXNSR0IArs4c6QAAAlFJREFUWEft1knITWEYB/DfR+ZQhgVWyoqNkkRkSMqUoQwLJaykDGUoCVkYU8hQLCTjwsqQspBiIQuUDCmJLJSQnSGu89a5ddzOfc853735Np7tM/z/7/uMHbVarbsulI7/BP7RD/THeSzG72zGO5OCiTiLuXhdsnx2YwfW4FwrBDrwAONwFctLEJiMm+iH9ynxZ3W/qj+wDBczoJPwsAmJUTiABQ36kILwC9vxsQqBngjMR2YC3se0JgTG4BQCyUa5gfV4V4XABhzOCbYoScn1SCoWpr/WK7wYS3GvagoG4hUG5wC9wFj8ipA4hE3pq092pgj3YmsEYC3ORPSDcA3T8bMqgRmpc+8IwAfMw5MSXfGXSawGQhHtx+ySQWu4lAyanXhb0kcegRGJcxgcK9GtbKCM3XccT8l/KfLPEhiALdiY9G+fIscS+gC+DycQSOVKnUAojssJ8yElAlc1CemYn7Tf8zzHOoE5uIK+VaOXsP+U1tGjGIGgG4ZdSQGtQjuOlG84lo7jr0UpyOpHI/R9aKvOSJj1F9JuCMsnKrE2nJKk5GCyycYXBcnob2Mbnpb1KdoFYf0uSbdXj0jQz+lqvlMWuG5XRKBudzRZJOsiwTfjSETf8kU0NF1GIVCjvEGYmj8iBNpyEYUDYk8OyIq0hZvht+0iCjPiJYZnkEJvT0jaN+yBRmn7RRQAVicETmeQZiYX0d0mT2/7RRRwwoB6jDArbqUjtqjw23IRZUFmpSfW1GbzPYdRyxdR0SuL9C1dREXBW9KXHUQtgcScu5zAH1FNzCFQ6MznAAAAAElFTkSuQmCC",
};
</script>
<style scoped>
.loading-box {
  width: 780px;
  height: 96px;
  background: url(https://assets.jiker.com/_for_common_project/2025/0109/admin/UBWRKcqoVEuq9DgU3ETRqlSxXZmslLR2hUf7B4y5.png)
    no-repeat center;
  margin: 10px auto;
  display: flex;
  align-items: center;
  justify-content: center;

  img {
    margin-right: 6px;
  }

  .loading-box-title {
    background: linear-gradient(35.90123076461183deg, #4ba1ff 0%, #5516ff 100%);
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
    font-family: PingFangSC, PingFang SC;
    font-weight: 500;
    font-size: 14px;
  }

  .loading,
  .loading > div {
    position: relative;
    box-sizing: border-box;
  }

  .loading {
    display: block;
    font-size: 0;
    color: #5516ff;
  }

  .loading > div {
    display: inline-block;
    float: none;
    background-color: currentColor;
    border: 0 solid currentColor;
  }

  .loading {
    width: 30px;
    height: 12px;
    padding-top: 3px;
  }

  .loading > div {
    width: 3px;
    height: 3px;
    margin: 2px;
    border-radius: 100%;
    opacity: 0;
    animation: ball-fall 1s ease-in-out infinite;
  }

  .loading > div:nth-child(1) {
    animation-delay: -200ms;
  }

  .loading > div:nth-child(2) {
    animation-delay: -100ms;
  }

  .loading > div:nth-child(3) {
    animation-delay: 0ms;
  }
}

@keyframes ball-fall {
  0% {
    opacity: 0;
    transform: translateY(-145%);
  }

  10% {
    opacity: 0.5;
  }

  20% {
    opacity: 1;
    transform: translateY(0);
  }

  80% {
    opacity: 1;
    transform: translateY(0);
  }

  90% {
    opacity: 0.5;
  }

  100% {
    opacity: 0;
    transform: translateY(145%);
  }
}

.header-title {
  font-family: PingFangSC, PingFang SC;
  font-weight: 400;
  font-size: 14px;
  color: rgba(0, 0, 0, 0.65);
  line-height: 26px;
  width: 400px;
  white-space: nowrap;
  /* 防止文本换行 */
  overflow: hidden;
  /* 隐藏溢出的文本 */
  text-overflow: ellipsis;
  /* 显示省略号 */
}

.header-line {
  width: 1px;
  height: 16px;
  background: rgba(0, 0, 0, 0.15);
  margin: 0 8px;
}

:deep(.el-form-item__label) {
  font-weight: 500;
  font-size: 14px;
  color: rgba(0, 0, 0, 0.85);
}

:deep(.markdown-body),
:deep(.v-note-wrapper .v-note-panel .v-note-show .v-show-content) {
  padding: 0px !important;
  background: transparent !important;
  box-shadow: none !important;
  min-height: 0 !important;
  min-width: 0 !important;
  font-size: 14px;
}

:deep(.markdown-body p) {
  margin-bottom: 0px !important;
  white-space: pre-wrap;
  font-size: 14px;
}

.add-tag-btn {
  width: 48px;
  height: 24px;
  line-height: 24px;
  text-align: center;
  background: #627aa3;
  font-family: PingFangSC, PingFang SC;
  font-weight: 400;
  font-size: 12px;
  color: #ffffff;
  flex-shrink: 0;
  cursor: pointer;
  margin-left: 8px;
  border: 1px solid #627aa3;

  &:hover {
    filter: brightness(1.1);
  }

  &.right {
    background-color: #fff;
    color: #627aa3;
  }
}

.footer-tips {
  text-align: center;
  font-family: PingFangSC, PingFang SC;
  font-weight: 400;
  font-size: 12px;
  color: rgba(0, 0, 0, 0.25);
  line-height: 20px;
  margin-top: 16px;
}

.drawer-box {
  background-color: #f5f5f5;
  padding: 0px;

  .drawer-footer-box {
    height: 188px;
    width: 100%;

    .section-footer {
      width: 780px;
      margin: 0 auto;
      display: flex;
      height: 172px;
      flex-direction: column-reverse;

      .footer-input-box {
        padding-bottom: 12px;
        overflow: hidden;

        .footer-input-container {
          padding-top: 6px;
          font-family: PingFangSC, PingFang SC;
          font-weight: 400;
          font-size: 14px;
          color: rgba(0, 0, 0, 0.85);
        }

        :deep(.el-textarea__inner) {
          box-shadow: none !important;
          padding-right: 6px !important;
          /* min-height: 56px !important; */
          border-top-right-radius: 10px;
          border-top-left-radius: 10px;
          resize: none;
          border: none;
        }

        &.active {
          border-color: #627aa3;
        }

        width: 100%;
        min-height: 56px;
        background: #fff;
        box-shadow: 0px 4px 8px 0px rgba(0, 0, 0, 0.06),
          0px 8px 23px -8px rgba(0, 0, 0, 0.08),
          0px -2px 4px -2px rgba(0, 0, 0, 0.04);
        border-radius: 10px;
        border: 1px solid #eeeeee;

        /* .footer-input {
          width: 100%;
          min-height: 56px;
        } */

        .input-btn-box {
          display: flex;
          align-items: center;
          justify-content: space-between;
          padding-right: 16px;
          cursor: pointer;

          .input-btn {
            border-radius: 5px;

            width: 40px;
            background: #fafafa;
            height: 40px;
            padding: 6px;
            text-align: center;

            &.active {
              background: #627aa3;

              &:hover {
                background: #4366a1;
              }
            }
          }
        }
      }
    }
  }

  .drawer-body-box {
    height: calc(100vh - 56px - 188px);
    padding: 40px 0 10px;
    overflow-y: auto;
    width: 100%;

    .section-content {
      width: 780px;
      margin: 0 auto;

      .message-item {
        display: flex;
        align-items: flex-start;
        margin-bottom: 16px;

        &.right {
          flex-direction: row-reverse;
        }

        .message-item-img {
          width: 48px;
          height: 48px;
          border-radius: 50%;
        }

        .message-item-content {
          max-width: 660px;
          min-height: 48px;
          background-color: #fff;
          border-radius: 4px;
          padding: 12px;

          .gpt-btn {
            .text {
              font-family: PingFangSC, PingFang SC;
              font-weight: 400;
              font-size: 14px;
              color: rgba(0, 0, 0, 0.45);
            }

            img {
              margin-right: 4px;
            }
          }

          .text {
            font-family: PingFangSC, PingFang SC;
            font-weight: 400;
            font-size: 14px;
            line-height: 24px;
          }

          &.right {
            .text {
              color: #ffffff;
            }

            :deep(.markdown-body) {
              /* margin-bottom: 0px !important; */
              /* white-space: pre-wrap; */
              color: #ffffff;
            }

            background-color: #646c78;
          }
        }
      }
    }
  }

  .drawer-header-box {
    width: 100%;
    height: 56px;
    background: #fff;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 16px 0 24px;

    .header-back {
      font-family: PingFangSC, PingFang SC;
      font-weight: 400;
      display: flex;
      align-items: center;
      font-size: 14px;
      color: rgba(0, 0, 0, 0.45);

      &:hover {
        color: rgba(0, 0, 0, 0.65);
      }

      cursor: pointer;
    }
  }
}

.section-container {
  background-color: #f5f5f5;
  height: 100%;
  width: 100%;
  padding: 12px 0 20px;

  .home-box {
    display: flex;
    align-items: flex-start;
    height: 100%;

    .home-content {
      width: 718px;
      height: 100%;
      margin-right: 16px;

      .home-content-img {
        width: 718px;
        height: 256px;
        cursor: pointer;
        background: linear-gradient(135deg, #ffefa9 0%, #d5ffd8 100%);
        background-size: contain;
        text-align: center;
        padding-top: 42px;
      }

      .home-content-img .title {
        font-family: PingFangSC, PingFang SC;
        font-weight: 600;
        font-size: 32px;
        color: #000000;
        line-height: 40px;
      }

      .home-content-img .tag {
        width: 132px;
        height: 26px;
        line-height: 26px;
        background: linear-gradient(270deg, #826600 0%, #345a00 100%);
        border-radius: 2px;
        font-family: PingFangSC, PingFang SC;
        font-weight: 400;
        font-size: 14px;
        color: #ffffff;
        margin: 4px auto 20px;
      }

      .home-content-img .text {
        font-family: PingFangSC, PingFang SC;
        font-weight: 400;
        font-size: 22px;
        color: #000000;
        margin-bottom: 22px;
      }

      .home-content-img .btn {
        width: 144px;
        height: 40px;
        line-height: 40px;
        background: #000;
        border-radius: 20px;
        font-family: PingFangSC, PingFang SC;
        font-weight: 500;
        margin: 0 auto;
        font-size: 14px;
        color: #ffffff;
      }

      .home-content-banner {
        width: 100%;
        height: 256px;
        object-fit: cover;
        background-color: aqua;
        margin-bottom: 20px;
      }

      .home-topic-box {
        background-color: #fff;
        margin-top: 20px;

        .topic-top {
          justify-content: space-between;
          height: 64px;
          padding: 14px 24px;
          border-bottom: 1px solid rgba(0, 0, 0, 0.06);

          .topic-sort {
            font-family: PingFangSC, PingFang SC;
            font-weight: 400;
            cursor: pointer;
            font-size: 14px;
            color: rgba(0, 0, 0, 0.65);
            position: relative;
            line-height: 22px;

            &.active {
              color: rgba(0, 0, 0, 0.85);
              color: #000000;
              font-weight: 500;

              &::after {
                position: absolute;
                content: " ";
                display: block;
                width: 18px;
                height: 2px;
                bottom: -8px;
                left: 6px;
                background: #0fc700;
                border-radius: 1px;
              }
            }
          }
        }

        .topic-list {
          width: 100%;
          min-height: 100px;

          &.none {
            height: 500px;
            display: flex;
            align-items: center;
            justify-content: center;

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

          /* padding: 0 24px; */

          .topic-item {
            max-height: 138px;
            min-height: 118px;
            width: 100%;
            padding: 16px 24px 0;
            /* border-bottom: 1px solid rgba(0, 0, 0, 0.06); */
            display: block;
            cursor: pointer;

            &:hover {
              background: #fafafa;
            }

            .topic-item-line {
              margin-top: 18px;
              width: 100%;
              height: 1px;
              background: rgba(0, 0, 0, 0.1);
            }

            .topic-item-title {
              font-family: PingFangSC, PingFang SC;
              font-weight: 500;
              font-size: 16px;
              color: rgba(0, 0, 0, 0.85);
              line-height: 24px;
              white-space: nowrap;
              overflow: hidden;
              text-overflow: ellipsis;
            }

            .topic-item-text {
              margin: 8px 0;
              font-family: PingFangSC, PingFang SC;
              font-weight: 400;
              font-size: 14px;
              color: rgba(0, 0, 0, 0.45);
              overflow: hidden;
              line-height: 22px;
              max-height: 44px;
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
                  cursor: pointer;
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

            &:last-child {
              border-bottom: none;
            }
          }
        }
      }
    }

    .home-right {
      height: 100%;
      flex-shrink: 0;
      width: 208px;
      /* position: fixed; */
      /* top: 0px; */

      /* right: 0px; */

      .home-right-item {
        margin: 12px 0;
        padding: 12px 16px;
        max-height: 440px;
        background-color: #fff;

        .label-list {
          display: flex;
          flex-wrap: wrap;

          .label-item {
            background: rgba(0, 0, 0, 0.04);
            font-family: PingFangSC, PingFang SC;
            font-weight: 400;
            font-size: 12px;
            color: rgba(0, 0, 0, 0.45);
            line-height: 14px;
            text-align: center;
            padding: 3px 6px;
            margin-right: 14px;
            margin-bottom: 8px;
          }
        }

        .popular-item {
          &:hover {
            background: rgba(0, 0, 0, 0.04);
          }

          font-family: PingFangSC, PingFang SC;
          font-weight: 400;
          font-size: 12px;
          color: rgba(0, 0, 0, 0.85);
          line-height: 20px;
          padding: 8px;
          background: rgba(0, 0, 0, 0.02);
          margin-bottom: 8px;
          cursor: pointer;

          &:last-child {
            margin-bottom: 0;
          }

          div {
            overflow: hidden;
            text-overflow: ellipsis;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
          }
        }

        .expert-item {
          margin-bottom: 12px;

          &:last-child {
            margin-bottom: 0;
          }

          .expert-item-img {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            object-fit: cover;
            /* background-color: #01c702; */
            margin-right: 8px;
          }

          .expert-item-name {
            font-family: PingFangSC, PingFang SC;
            font-weight: 500;
            font-size: 12px;
            color: rgba(0, 0, 0, 0.85);
            line-height: 20px;
          }

          .expert-item-text {
            font-family: PingFangSC, PingFang SC;
            font-weight: 400;
            font-size: 12px;
            color: rgba(0, 0, 0, 0.45);
            line-height: 20px;
          }
        }

        .title {
          font-family: PingFangSC, PingFang SC;
          font-weight: 500;
          font-size: 16px;
          color: rgba(0, 0, 0, 0.85);
          line-height: 24px;
          margin-bottom: 12px;
        }
      }

      .home-right-statistics {
        background-color: #fff;
        padding: 14px 20px 16px;
        display: flex;
        align-items: center;
        justify-content: space-between;

        .number {
          font-family: PingFangSC, PingFang SC;
          font-weight: 500;
          font-size: 16px;
          color: #262626;
          line-height: 24px;
        }

        .text {
          font-family: PingFangSC, PingFang SC;
          font-weight: 400;
          font-size: 12px;
          color: rgba(0, 0, 0, 0.45);
          line-height: 20px;
        }
      }

      .home-right-user {
        padding: 16px;
        background-color: #fff;
        border-bottom: 1px solid rgba(0, 0, 0, 0.06);

        .user-img {
          width: 56px;
          height: 56px;
          margin-right: 8px;
          border-radius: 50%;
          /* background-color: #01c702; */
        }

        .user-name {
          font-family: PingFangSC, PingFang SC;
          font-weight: 500;
          font-size: 16px;
          color: rgba(0, 0, 0, 0.85);
          line-height: 26px;
        }

        .user-job {
          font-family: PingFangSC, PingFang SC;
          font-weight: 400;
          font-size: 12px;
          color: rgba(0, 0, 0, 0.45);
          margin-top: 4px;
          line-height: 20px;
        }
      }

      .home-right-btn {
        width: 208px;
        height: 40px;
        line-height: 40px;
        background: linear-gradient(270deg, #21b3d6 0%, #01c702 100%);
        border-radius: 4px;
        text-align: center;
        font-family: PingFangSC, PingFang SC;
        font-weight: 500;
        font-size: 14px;
        color: #ffffff;
        cursor: pointer;
        margin-bottom: 8px;
        display: flex;
        align-items: center;
        justify-content: center;

        img {
          width: 16px;
          height: 16px;
          margin-right: 4px;
        }

        &:hover {
          filter: brightness(1.1);
        }
      }
    }
  }
}

.flex {
  display: flex;
  align-items: center;
}

:deep(.page-body-container) {
  background: #f5f5f5 !important;
}
</style>

<style>
.el-textarea__inner:focus {
  box-shadow: 0 0 0 1px #000 inset;
}

.el-drawer__body {
  padding: 0px !important;
}

.el-select__wrapper {
  font-weight: 500;
  font-size: 14px;
  color: #000000;
}

.el-select-dropdown__item.is-selected {
  color: #000000;
}

.el-select-dropdown__item.is-hovering {
  background: #fafafa;
}

.el-select__wrapper.is-focused {
  box-shadow: 0 0 0 1px #000000 inset;
}

.page-body-container {
  background: #f5f5f5 !important;
}

.el-select-dropdown__wrap {
  max-height: 150px !important;
  overflow: auto;
}

/* .page-header-item:nth-child(3) {
    display: none;
  } */

.dialog-box {
  padding: 0px;

  .el-dialog__header {
    padding: 16px 24px !important;
    border-bottom: 1px solid rgba(0, 0, 0, 0.06);

    .el-dialog__title {
      font-family: PingFangSC, PingFang SC;
      font-weight: 500;
      font-size: 16px;
      color: rgba(0, 0, 0, 0.85);
    }
  }

  .el-dialog__body {
    padding: 24px;
  }

  .el-dialog__footer {
    padding: 8px 16px 12px;
    border-top: 1px solid rgba(0, 0, 0, 0.06);
  }
}
</style>
