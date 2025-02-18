<template>
    <div class="section-container" v-loading="chatLoading" @click.stop="inputClick = false">
      <div class="section-header-box">
        <div class="section-header">
          <div class="flex">
            <div class="section-header-back" @click="handleBack">
              <el-icon>
                <ArrowLeft />
              </el-icon>
              返回
            </div>
            <div class="header-line"></div>
            <div class="header-title">{{ topicInfo.title }}</div>
          </div>
          <div class="base-box header flex">
            <div class="flex" @click="handleTopicAction(1, topicInfo)" style="cursor:pointer">
              <img
                :src="!topicInfo.favorite_active ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/AnhT4Inxw4lkcEF6spx5WFJImQdmS3VS6LUgbEsL.svg' : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/hFiJogbyXyfqh3S2cFJrIvOd0uiqyRHln32LahWJ.svg'"
                alt="" />
              <div class="text">{{ topicInfo.favorite.length || '喜欢' }}</div>
            </div>
            <div class="flex" style="margin-left: 10px;cursor:pointer" @click="handleTopicAction(2, topicInfo)">
              <img
                :src="!topicInfo.collect_active ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/zzRTqjWwocSpW41IHUWuk1hpxcmyZnYnjl78vf45.svg' : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/YRXLLtRFV2Dqog9MWzqRTOKewC35IDYzd1IyAHVD.svg'"
                alt="" />
              <div class="text">{{ topicInfo.collect.length || '收藏' }}</div>
            </div>
            <div class="flex" style="margin-left: 10px;cursor:pointer">
              <img
                src="https://assets.jiker.com/_for_common_project/2024/1227/admin/3Pq7pt2sqPDEW9EiPKM6SvGYxO9Cws6FW8N0Cn0e.svg"
                alt="" />
              <div class="text">{{ topicInfo.user_ids.length }}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="section-content-box" ref="scrollContainer">
        <div class="section-content">
          <div v-for="(item, index) in messageList" :key="index" style="margin-bottom:24px">
            <div v-if="(userInfo.id == (item.user_info || {}).id)">
              <div>
                <div :id="(item.user_message || {}).id" class="message-item right">
                  <div class="flex">
                    <img class="message-item-img"
                      :src="(item.user_info || {}).avatar_url || 'https://assets.jiker.com/_for_common_project/2024/1212/admin/IlIPFyfVqw4sYSqaiWYLQOYxQD3vF21CcxrmwifC.png'" />
                    <div class="message-item-base">
                      <div class="user-name">{{ (item.user_info || {}).name }}</div>
                      <div class="item-time">{{ (item.user_message || {}).created_at }}</div>
                    </div>
                  </div>
                  <div :class="['message-item-content',`${item.user_message.role == 'user' ? 'user':''}`]">
                    <div class="text">
                      <Markdown v-model="item.user_message.content" :toolbarsFlag="false" />
                    </div>
                  </div>
                </div>
                <div v-if="(item.user_message.pre_user || []).length" class="flex"
                  style="flex-direction: row-reverse;margin-bottom:8px">
                  <div style="width:184px;"></div>
                  <div class="flex" @click="handleScroll(item)">
                    <div class="user-item flex" v-for="user in item.user_message.pre_user" :key="user.id">
                      <img class="user-item-img"
                        :src="user.avatar_url || 'https://assets.jiker.com/_for_common_project/2024/1212/admin/IlIPFyfVqw4sYSqaiWYLQOYxQD3vF21CcxrmwifC.png'" />1
                    </div>
  
                  </div>
  
                </div>
              </div>
              <div :id="item.assistant_message.id" v-if="item.assistant_message.content">
                <div class="message-item right">
                  <div style="width:184px;"></div>
                  <div>
                    <div
                      :class="['message-item-content',`${item.assistant_message.role == 'user' ? 'user':''}`,pre_message_id == item.assistant_message.id ? 'animation':'']">
                      <div class="text">
                        <Markdown v-model="item.assistant_message.content" :toolbarsFlag="false" />
                      </div>
                      <div class="base-box flex" style="justify-content:space-between">
                        <div class="flex">
                          <div class="flex" @click="handleCopy(item.assistant_message.content)" style="cursor:pointer">
                            <!-- <el-icon size="11" color="#5C5C5C" style="margin-right:4px;">
                          <DocumentCopy />
                        </el-icon> -->
                            <img src="https://assets.jiker.com/_for_common_project/2024/1227/admin/zfN6Z1BXawNf9UvNhJjPLoFW8u8iDFYcG6j5Yb3f.svg" />
                            <div class="text">复制</div>
                          </div>
                          <div class="flex" @click="handleTopicMessageAction(1, item.assistant_message)"
                            style="cursor:pointer;margin-left: 10px;">
                            <img
                          :src="!item.assistant_message.favorite_active ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/AnhT4Inxw4lkcEF6spx5WFJImQdmS3VS6LUgbEsL.svg' : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/hFiJogbyXyfqh3S2cFJrIvOd0uiqyRHln32LahWJ.svg'"
                          alt="" />
                            <div class="text">{{ item.assistant_message.favorite.length || '喜欢' }}</div>
                          </div>
                          <div class="flex" style="margin-left: 10px;cursor:pointer"
                            @click="handleTopicMessageAction(2, item.assistant_message)">
                            <img
                          :src="!item.assistant_message.collect_active ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/zzRTqjWwocSpW41IHUWuk1hpxcmyZnYnjl78vf45.svg' : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/YRXLLtRFV2Dqog9MWzqRTOKewC35IDYzd1IyAHVD.svg'"
                          alt="" />
                            <div class="text">{{ item.assistant_message.collect.length || '收藏' }}</div>
                          </div>
                        </div>
                        <el-tooltip class="box-item" effect="dark" content="在此回答的基础上继续提问" placement="top">
                          <div class="quote-btn" @click.stop="handleQuote(item.assistant_message,index,null)"
                            style="cursor:pointer;margin-left: 10px;">
                            追问
                          </div>
                        </el-tooltip>
                      </div>
                    </div>
                    <div class="ai-info">内容由AI大模型生成，请仔细甄别</div>
                  </div>
  
  
                </div>
              </div>
  
              <div class="message-item-quote flex" v-if="quoteId == item.assistant_message.id"
                @click.stop="inputClick = true">
                <div style="width:192px;"></div>
                <div :class="['quote-item-box', inputClick ? 'active' : '']">
                  <el-input class="footer-input" :id="`input-${item.assistant_message.id}`" v-model="message"
                    placeholder="一个优秀的追问，往往能发现新的价值～" :autosize="autosizeConfig" type="textarea"></el-input>
                  <div ref="hiddenDiv" style="visibility: hidden; white-space: pre; position: absolute;font-size:14px;">
                    {{ message }}
                  </div>
                  <div class="input-btn-box">
                    <div></div>
                    <div :class="['input-btn', message ? 'active' : '']" @click="handleSend">
                      <img v-show="!message"
                  src="https://assets.jiker.com/_for_common_project/2024/0920/admin/VzHJBpFvtZbsGKuwqKj8J6dx14I1F1nWT4pcxNGf.svg" />
                      <img v-show="message"
                  src="https://assets.jiker.com/_for_common_project/2024/0920/admin/KWDF0jvvqHbFvurohb76XAKc6Y9fwtoaQXGgYl3q.svg" />
                    </div>
                  </div>
  
                </div>
  
                <el-tooltip class="box-item" effect="dark" content="删除" placement="top">
                  <div class="item-quote-close" @click="quoteId=null,message = null"></div>
                </el-tooltip>
  
              </div>
  
              <div v-if="(currentIndex+1) == index && btnLoading" class="loading-box">
                <img src="https://assets.jiker.com/_for_common_project/2025/0109/admin/iGHt0dZGwCb6isZWjaOkFwdUZtmlZoc6FiZRkbs3.svg" />
                <div style="display:flex;align-items: flex-end">
                  <div class="loading-box-title">{{btnLoadingText}}</div>
                  <div class="loading">
                    <div></div>
                    <div></div>
                    <div></div>
                  </div>
                </div>
              </div>
            </div>
            <div v-else>
              <div :id="item.user_message.id">
                <div class="message-item">
                  <div class="flex">
                    <div class="message-item-base">
                      <div class="user-name">{{ (item.user_info || {}).name }}</div>
                      <div class="item-time">{{ (item.user_message || {}).created_at }}</div>
                    </div>
                    <img class="message-item-img"
                      :src="(item.user_info || {}).avatar_url || 'https://assets.jiker.com/_for_common_project/2024/1212/admin/IlIPFyfVqw4sYSqaiWYLQOYxQD3vF21CcxrmwifC.png'" />
                  </div>
                  <div
                    :class="['message-item-content',`${item.user_message.role == 'user' ? 'user':''}`,pre_message_id == item.id ? 'animation':'']">
                    <div class="text">
                      <Markdown v-model="item.user_message.content" :toolbarsFlag="false" />
                    </div>
                  </div>
                </div>
                <div v-if="(item.user_message.pre_user || []).length" class="flex" style="margin-bottom:8px">
                  <div style="width:184px;"></div>
                  <div class="flex" @click="handleScroll(item)">
                    <div class="user-item flex" v-for="user in item.user_message.pre_user" :key="user.id">
                      <img class="user-item-img"
                        :src="user.avatar_url || 'https://assets.jiker.com/_for_common_project/2024/1212/admin/IlIPFyfVqw4sYSqaiWYLQOYxQD3vF21CcxrmwifC.png'" />1
                    </div>
  
                  </div>
  
                </div>
              </div>
              <div :id="item.assistant_message.id">
                <div class="message-item">
                  <div style="width:184px;"></div>
                  <div>
                    <div
                      :class="['message-item-content',`${item.assistant_message.role == 'user' ? 'user':''}`,pre_message_id == item.assistant_message.id ? 'animation':'']">
                      <div class="text">
                        <Markdown v-model="item.assistant_message.content" :toolbarsFlag="false" />
                      </div>
                      <div class="base-box flex" style="justify-content:space-between">
                        <div class="flex">
                          <div class="flex" @click="handleCopy(item.assistant_message.content)" style="cursor:pointer">
                            <img src="https://assets.jiker.com/_for_common_project/2024/1227/admin/zfN6Z1BXawNf9UvNhJjPLoFW8u8iDFYcG6j5Yb3f.svg" />
                            <div class="text">复制</div>
                          </div>
                          <div class="flex" @click="handleTopicMessageAction(1, item.assistant_message)"
                            style="cursor:pointer;margin-left: 10px;">
                            <img
                          :src="!item.assistant_message.favorite_active ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/AnhT4Inxw4lkcEF6spx5WFJImQdmS3VS6LUgbEsL.svg' : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/hFiJogbyXyfqh3S2cFJrIvOd0uiqyRHln32LahWJ.svg'"
                          alt="" />
                            <div class="text">{{ item.assistant_message.favorite.length || '喜欢' }}</div>
                          </div>
                          <div class="flex" style="margin-left: 10px;cursor:pointer"
                            @click="handleTopicMessageAction(2, item.assistant_message)">
                            <img
                          :src="!item.assistant_message.collect_active ? 'https://assets.jiker.com/_for_common_project/2024/1227/admin/zzRTqjWwocSpW41IHUWuk1hpxcmyZnYnjl78vf45.svg' : 'https://assets.jiker.com/_for_common_project/2024/1227/admin/YRXLLtRFV2Dqog9MWzqRTOKewC35IDYzd1IyAHVD.svg'"
                          alt="" />
                            <div class="text">{{ item.assistant_message.collect.length || '收藏' }}</div>
                          </div>
                        </div>
                        <el-tooltip class="box-item" effect="dark" content="在此回答的基础上继续提问" placement="top">
                          <div class="quote-btn" @click.stop="handleQuote(item.assistant_message,index,null)"
                            style="cursor:pointer;margin-left: 10px;">
                            追问
                          </div>
                        </el-tooltip>
                      </div>
                    </div>
                    <div class="ai-info" style="text-align:left;">内容由AI大模型生成，请仔细甄别</div>
                  </div>
                </div>
  
  
  
                <div class="message-item-quote flex" v-if="quoteId == item.assistant_message.id"
                  @click.stop="inputClick = true">
                  <div style="width:184px;"></div>
                  <div :class="['quote-item-box', inputClick ? 'active' : '']">
                    <el-input class="footer-input" :id="`input-${item.assistant_message.id}`" v-model="message"
                      placeholder="一个优秀的追问，往往能发现新的价值～" :autosize="{ minRows: 1, maxRows: 3 }" type="textarea"></el-input>
                    <div class="input-btn-box">
                      <div></div>
                      <div :class="['input-btn', message ? 'active' : '']" @click="handleSend" v-loading="btnLoading">
                        <img v-show="!message"
                  src="https://assets.jiker.com/_for_common_project/2024/0920/admin/VzHJBpFvtZbsGKuwqKj8J6dx14I1F1nWT4pcxNGf.svg" />
                        <img v-show="message"
                  src="https://assets.jiker.com/_for_common_project/2024/0920/admin/KWDF0jvvqHbFvurohb76XAKc6Y9fwtoaQXGgYl3q.svg" />
                      </div>
                    </div>
  
                  </div>
  
                  <el-tooltip class="box-item" effect="dark" content="删除" placement="top">
                    <div class="item-quote-close" @click="quoteId=null,message = null"></div>
                  </el-tooltip>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- <div class="section-footer-box" v-if="!quoteId">
      <div class="section-footer">
        <div class="footer-tips">内容由 AI 大模型生成，请仔细甄别</div>
  
        <div :class="['footer-input-box', message ? 'active' : '']">
          <div class="footer-input-container">
            <el-input class="footer-input" v-model="message" placeholder="一个优秀的追问，往往能发现新的价值～" :autosize="{ minRows: 1, maxRows: 3 }"
              type="textarea"></el-input>
          </div>
          <div class="input-btn-box">
            <div></div>
            <div :class="['input-btn', message ? 'active' : '']" @click="handleSend" v-loading="btnLoading">
              <img v-show="!message"
                  src="https://assets.jiker.com/_for_common_project/2024/0920/admin/VzHJBpFvtZbsGKuwqKj8J6dx14I1F1nWT4pcxNGf.svg" />
              <img v-show="message"
                  src="https://assets.jiker.com/_for_common_project/2024/0920/admin/KWDF0jvvqHbFvurohb76XAKc6Y9fwtoaQXGgYl3q.svg" />
            </div>
          </div>
  
        </div>
      </div>
    </div> -->
    </div>
  </template>
  <script setup lang="ts">
    import { fetchEventSource } from "fetch-event-source";
  import { ElMessage, ElMessageBox } from "element-plus";
  
  import mixAxios from "mix-axios";
  import { reactive, ref,nextTick,watch,computed } from "vue";
  const message = ref("");
  const newTag = ref("");
  const referer = ref("");
  const pre_message_id = ref("");
  const currentIndex = ref("");
  const currentChildIndex = ref("");
  const topic_id = ref<any>(null);
  const dialogVisible = ref(false);
  const btnLoading = ref(false);
  const intervalId = ref();
  const btnLoadingText = ref("问题已接收");
  
  const inputClick = ref(false);
  
  const topicInfo = ref({
    favorite: [],
    collect: [],
    user_ids: []
  })
  const userInfo = ref({})
  const scrollContainer = ref(null);
  const hiddenDiv = ref(null);
  const minRows = ref(1)
  // const currentMessage =ref<any>(null)
  const quoteId = ref<any>(null)
  const autosizeConfig = computed(() => ({
    minRows:minRows.value,
    maxRows:3,
  }));
  const tagList = ref([]);
  const formData = reactive({
    title: "",
    tags: [] as any,
    is_open: 2,
  });
  const formTagStatus = ref(false);
  const chatLoading = ref(false);
  const messageList = ref([
    // { 
    //   content: "Hey，我是 Jiker～ 你可以和我聊天！",
    //   user_message:{
    //     favorite: [],
    //     collect: [],
    //   },
    //   assistant_message:{
    //     favorite: [],
    //     collect: [],
    //   },
    //   pre_messages:[] ,
    //   role: "assistant" 
    // }
  ]);
  watch(message, (newCount, oldCount) => {
      minRows.value = hiddenDiv.value[0].offsetWidth > 676 ? 2 :1
    }, {
      deep: true 
  });
  watch(() => btnLoading.value, (newVal, oldVal) => {
    if(newVal){
    console.log(btnLoadingText.value)
  
      intervalId.value = setInterval(handleUpdateLoading, 2000);
    }else{
      btnLoadingText.value="问题已接收";
    }
  }, { deep: true });
  
  const handleUpdateLoading = () => {
    let list =  ['问题已接收','理解问题','正在思考中']
    console.log(2222)
  
    let index = list.findIndex(item=> btnLoadingText.value == item)
    if(index == 2){
      clearInterval(intervalId.value);
      btnLoadingText.value="问题已接收";
  
    }else{
      btnLoadingText.value = list[index+1]
    }
  }
  const handleShowAddTag = (e) => {
    if (e[e.length - 1].name == "添加选项") {
      formTagStatus.value = true;
    }
  };
  const handleBack = () => { 
    window.location.href = referer.value;
  }
  const handleScroll = (data) => {
    const targetElement = document.getElementById(data.user_message.pre_message_id);
    
    if (targetElement && scrollContainer.value) {
      const targetTop = targetElement.offsetTop - scrollContainer.value.offsetTop - 20;
      scrollContainer.value.scrollTo({
        top: targetTop,
        behavior: 'smooth'
      });
      // setTimeout(()=> {
        pre_message_id.value = data.user_message.pre_message_id;
      // }, 1000);
      setTimeout(()=> {
        pre_message_id.value = null
  
      }, 8000);
    }
  }
  const handleSend = () => {
    if (!message.value) {
      return
    }
    const abortController = new AbortController();
    const url = `https://gpt-service.jiker.cc/api/v2/chat`;
    let messageDate = "";
    if (btnLoading.value) {
      return;
    }
    const params = {
      role: "user",
      content: message.value,
      topic_id: topic_id.value,
      pre_message_id: quoteId.value
    }
  
    mixAxios.post("/app-api/community/topic/message", params).then(res => {
      btnLoading.value = true;
      messageList.value.splice(currentIndex.value+1,0,{
        assistant_message:{
          favorite: [],
          collect: [],
        },
        user_message:{...res},
        user_info: userInfo.value,
      })
      const gpt_params = {
        stream: true,
        messages: [{role:'user',content:message.value}]
      };
      message.value = "";
  
      // setTimeout(function firstFunction() {
      //   scrollContainer.value.scrollTo({
      //     top: scrollContainer.value.scrollHeight+50,
      //     behavior: 'smooth'
      //   });
      // }, 1000);
      messageList.value.forEach(item=>{
        if(item.assistant_message.id == quoteId.value){
          gpt_params.messages.unshift({
            role: item.assistant_message.role,
            content: item.assistant_message.content,
          })
        }
      })
      quoteId.value = -1;
      fetchEventSource(url, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Token: "1df65034830b214078fe",
        },
        body: JSON.stringify(gpt_params),
        signal: abortController.signal,
        onopen() {
          message.value = "";
          quoteId.value = -1;
          // setTimeout(function firstFunction() {
          //   scrollContainer.value.scrollTo({
          //     top: scrollContainer.value.scrollHeight+50,
          //     behavior: 'smooth'
          //   });
          // }, 1000);
        },
        onmessage(event) {
          const data = JSON.parse(event.data);
          if (data.choices[0].finish_reason == "stop") {
            abortController.abort();
            mixAxios.post("/app-api/community/topic/message", { content: messageDate, role: "assistant", topic_id: topic_id.value, pre_message_id: res.id }).then(res => {
              messageList.value[currentIndex.value+1].assistant_message = res
            })
            // setTimeout(function firstFunction() {
            //   scrollContainer.value.scrollTo({
            //     top: scrollContainer.value.scrollHeight+50,
            //     behavior: 'smooth'
            //   });
            // }, 1000);
            btnLoading.value = false;
            btnLoadingText.value="问题已接收";
          } else {
            messageDate += data.choices[0].delta?.content;
            messageList.value[currentIndex.value+1].assistant_message.content = messageDate;
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
    })
  
  
  };
  const getData = () => {
    chatLoading.value = true;
    mixAxios.get(`/app-api/community/topic/${topic_id.value}`).then(res => {
      topicInfo.value = res;
      messageList.value = res.messages.map(message => { 
        message.assistant_message.collect_active = Boolean(message.assistant_message.collect.filter(j => j.user_id == userInfo.value.id).length);
        message.assistant_message.favorite_active = Boolean(message.assistant_message.favorite.filter(j => j.user_id == userInfo.value.id).length);
        return message
      })
      topicInfo.value.collect_active = Boolean(res.collect.filter(j => j.user_id == userInfo.value.id).length);
      topicInfo.value.favorite_active = Boolean(res.favorite.filter(j => j.user_id == userInfo.value.id).length);
      setTimeout(function firstFunction() {
        chatLoading.value = false;
      }, 1000);
  
    })
  }
  
  const getUserInfo = () => {
    mixAxios.get("/api/user/info").then(res => {
      userInfo.value = res;
      getData()
  
    })
  }
  const handleTopicMessageAction = (type, data) => {
    const params = {
      topic_id: data.topic_id,
      topic_message_id: data.id,
      type
    }
    mixAxios.post('/app-api/community/topic/actions', params).then(() => {
      ElMessage.success("操作成功！");
      getNewData();
    })
  }
  const handleTopicAction = (type, data) => {
    const params = {
      topic_id: data.id,
      type
    }
    mixAxios.post('/app-api/community/topic/actions', params).then(() => {
      ElMessage.success("操作成功！");
      getNewData();
    })
  }
  const getNewData = () => {
    mixAxios.get(`/app-api/community/topic/${topic_id.value}`).then(res => {
      topicInfo.value = res;
      messageList.value = res.messages.map(message => { 
        message.assistant_message.collect_active = Boolean(message.assistant_message.collect.filter(j => j.user_id == userInfo.value.id).length);
        message.assistant_message.favorite_active = Boolean(message.assistant_message.favorite.filter(j => j.user_id == userInfo.value.id).length);
        return message
      })
      topicInfo.value.collect_active = Boolean(res.collect.filter(j => j.user_id == userInfo.value.id).length);
      topicInfo.value.favorite_active = Boolean(res.favorite.filter(j => j.user_id == userInfo.value.id).length);
  
    })
  }
  const handleCopy = (item) => {
    let input = "";
    if (item.indexOf("```")) {
      input = document.createElement("textarea");
    } else input = document.createElement("input");
    input.setAttribute("readonly", "readonly");
    input.value = item;
    document.body.appendChild(input);
    input.select();
    if (document.execCommand("copy")) document.execCommand("copy");
    document.body.removeChild(input);
    ElMessage.success("复制成功")
  }
  const handleQuote = (data,index,childIndex) =>{
    quoteId.value = data.id;
    currentIndex.value = index;
    currentChildIndex.value = childIndex;
    inputClick.value = true;
    nextTick(()=>{
      const inputElement = document.getElementById(`input-${data.id}`);
       if (inputElement) {
          inputElement.focus();
        }
    })
  }
  const currentUrl = new URL(window.location.href);
  topic_id.value = currentUrl.searchParams.get('id') || 177;
  referer.value = currentUrl.searchParams.get('referer');
  // topic_id.value = 51;
  
  getUserInfo()
  </script>
  <style scoped>
    .ai-info {
      text-align: right;
      margin-top: 8px;
      font-family: PingFangSC, PingFang SC;
      font-weight: 400;
      font-size: 12px;
      color: rgba(0, 0, 0, 0.25);
      line-height: 20px;
    }
  
    .loading-box {
      width: 660px;
      height: 96px;
      background: url(https://assets.jiker.com/_for_common_project/2025/0109/admin/UBWRKcqoVEuq9DgU3ETRqlSxXZmslLR2hUf7B4y5.png) no-repeat center;
      margin: 10px auto;
      display: flex;
      align-items: center;
      justify-content: center;
  
      img {
        margin-right: 6px;
      }
  
      .loading-box-title {
        background: linear-gradient(35.90123076461183deg, #4BA1FF 0%, #5516FF 100%);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
        font-family: PingFangSC, PingFang SC;
        font-weight: 500;
        font-size: 14px;
      }
  
      .loading,
      .loading>div {
        position: relative;
        box-sizing: border-box;
      }
  
      .loading {
        display: block;
        font-size: 0;
        color: #5516FF;
      }
  
      .loading>div {
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
  
      .loading>div {
        width: 3px;
        height: 3px;
        margin: 2px;
        border-radius: 100%;
        opacity: 0;
        animation: ball-fall 1s ease-in-out infinite;
      }
  
      .loading>div:nth-child(1) {
        animation-delay: -200ms;
      }
  
      .loading>div:nth-child(2) {
        animation-delay: -100ms;
      }
  
      .loading>div:nth-child(3) {
        animation-delay: 0ms;
      }
    }
  
    .quote-btn {
      width: 40px;
      height: 20px;
      line-height: 20px;
      border-radius: 10px;
      border: 1px solid #627AA3;
      text-align: center;
      font-family: PingFangSC, PingFang SC;
      font-weight: 400;
      font-size: 12px;
      color: #627AA3;
      display: none;
  
      &:hover {
        background: #627AA3;
        color: #fff;
      }
    }
  
    .user-item {
      height: 20px;
      line-height: 20px;
      background: #fff;
      border-radius: 10px;
      padding: 0 8px 0 6px;
      font-family: PingFangSC, PingFang SC;
      font-weight: 400;
      font-size: 12px;
      color: rgba(0, 0, 0, 0.45);
      margin-right: 8px;
      cursor: pointer;
  
      .user-item-img {
        width: 16px;
        margin-right: 4px;
        border-radius: 50%;
        height: 16px;
      }
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
      font-family: PingFangSC, PingFang SC;
      font-weight: 500;
      font-size: 14px;
      color: rgba(0, 0, 0, 0.85);
    }
  
    :deep(.el-input__wrapper) {
      border-radius: 2px;
  
      &.is-focus {
        box-shadow: 0 0 0 1px #627aa3 inset;
      }
    }
  
    .section-footer-box {
      height: 188px;
      width: 100%;
      background-color: #f5f5f5;
  
      .section-footer {
        width: 780px;
        margin: 0 auto;
        /* header:100%; */
        display: flex;
        flex-direction: column-reverse;
        /* justify-content: space-between; */
        height: 172px;
  
        .footer-tips {
          text-align: center;
          font-family: PingFangSC, PingFang SC;
          font-weight: 400;
          font-size: 12px;
          color: rgba(0, 0, 0, 0.25);
          line-height: 20px;
          margin-top: 16px;
        }
  
        .footer-input-box {
          padding-bottom: 12px;
          overflow: hidden;
  
          .footer-input-container {
            padding-top: 6px;
            /* flex-grow: 1; */
  
            /* margin-bottom: 2px; */
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
              }
            }
          }
        }
      }
    }
  
    .section-container {
      padding-top: 30px;
      background-color: #f5f5f5;
      height: calc(100vh - 40px) !important;
      width: 100%;
  
      .section-content-box {
        height: calc(100vh - 124px);
        padding: 30px 0 20px;
        overflow-y: auto;
        width: 100%;
  
        .section-content {
          width: 1034px;
          margin: 0 auto;
  
          .message-item {
            display: flex;
            align-items: flex-start;
            margin-bottom: 8px;
  
            &.right {
              flex-direction: row-reverse;
  
              .message-item-content {
                border-radius: 8px 0px 8px 8px;
              }
  
              :deep(.markdown-body) {
                color: #000000;
              }
  
              .message-item-base {
                text-align: left;
              }
  
              .message-item-img {
                margin: 0px 8px 0 12px;
              }
  
            }
  
            .message-item-base {
              text-align: right;
              padding-top: 2px;
  
              .user-name {
                font-family: PingFangSC, PingFang SC;
                font-weight: 400;
                font-size: 14px;
                line-height: 22px;
                color: #595959;
                margin-bottom: 2px;
              }
  
              .item-time {
                font-family: PingFangSC, PingFang SC;
                font-weight: 400;
                font-size: 12px;
                line-height: 20px;
                color: rgba(0, 0, 0, 0.45);
              }
            }
  
  
  
            .message-item-img {
              width: 48px;
              height: 48px;
              margin: 0px 12px 0 8px;
              border-radius: 50%;
            }
  
            .message-item-content {
              max-width: 660px;
              min-height: 48px;
              background-color: #fff;
              padding: 12px 16px;
              border-radius: 0px 8px 8px 8px;
              border: 1px solid #fff;
  
              &.animation {
                animation: animationBorder 4s infinite;
              }
  
              &:hover {
                box-shadow: 0 8px 16px 0 rgba(48, 55, 66, 0.15);
                transform: translate(0, -1px);
  
                .quote-btn {
                  display: block;
                }
              }
  
              &.user {
                border-radius: 8px;
                background: #646C78;
                border: 1px solid #646C78;
  
                :deep(.markdown-body) {
                  color: #fff;
                }
  
                :deep(.markdown-body pre code) {
                  color: #000 !important;
                }
  
                :deep(.markdown-body code) {
                  /* color:#000; */
                }
              }
  
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
                /* :deep(.markdown-body) {
                  color: #ffffff;
                }
  
                background-color: #646c78; */
                flex-direction: row-reverse;
  
              }
            }
          }
        }
      }
  
      .section-header-box {
        height: 54px;
        width: 100%;
  
        .section-header {
  
          width: 1180px;
          height: 54px;
          line-height: 54px;
          background: #FFFFFF;
          border-radius: 27px;
          border: 1px solid #E5E5E5;
          margin: 0 auto;
          display: flex;
          align-items: center;
          justify-content: space-between;
          padding: 0 32px 0 24px;
  
          .section-header-back {
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
    }
  
    .flex {
      display: flex;
      align-items: center;
    }
  
    .section-btn {
      &.circle {
        border-radius: 16px;
      }
  
      height: 32px;
      padding: 0 16px;
      line-height: 32px;
      text-align: center;
      font-family: PingFangSC,
      PingFang SC;
      font-weight: 400;
      font-size: 14px;
      color: #ffffff;
      background: #00c700;
      border-radius: 2px;
      cursor: pointer;
  
      &:hover {
        filter: brightness(1.1);
      }
    }
  
    /* :deep(.el-textarea__inner) {
      box-shadow: none !important;
      padding: 16px 20px;
      min-height: 76px !important;
      height: 76px !important;
      border-top-right-radius: 10px;
      border-top-left-radius: 10px;
      resize: none;
      border: none;
    } */
  
    :deep(.markdown-body),
    :deep(.v-note-wrapper .v-note-panel .v-note-show .v-show-content) {
      padding: 0px !important;
      font-size: 14px;
      background: transparent !important;
      box-shadow: none !important;
      min-height: 0 !important;
      min-width: 0 !important;
    }
  
    :deep(.markdown-body code) {
      font-size: 100%;
    }
  
    :deep(.markdown-body p) {
      font-size: 14px;
      margin-bottom: 0px !important;
      white-space: pre-wrap;
  
    }
  
    .base-box {
      margin-top: 6px;
  
      &>div>div {
        &:hover {
          .text {
            color: rgba(0, 0, 0, 0.65);
          }
        }
  
      }
  
      &.header {
        margin-top: 0px;
  
        .text {
          font-size: 14px;
        }
  
        img {
          margin-right: 4px;
          width: 18px;
          height: 18px;
        }
      }
  
      .text {
        font-family: PingFangSC, PingFang SC;
        font-weight: 400;
        font-size: 12px !important;
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
        width: 12px;
        height: 12px;
        cursor: pointer;
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
  </style>
  
  
  <style>
    .message-item-quote {
      width: 100%;
      margin-top: 12px;
      align-items: flex-start !important;
  
      .el-textarea__inner {
        box-shadow: none !important;
        padding: 6px 16px 0;
        /* min-height: 76px !important;
          height: 76px !important; */
        border-top-right-radius: 10px;
        border-top-left-radius: 10px;
        resize: none;
        border: none;
      }
  
      .item-quote-close {
        margin-top: 10px;
        /* background: url("https://assets.jiker.com/_for_common_project/2024/1227/admin/AtXEktpIYnEmH2ArrqWfy0QyJiJoZeMZGIc7cHHf.svg"); */
        background: url(https://assets.jiker.com/_for_common_project/2024/1227/admin/AtXEktpIYnEmH2ArrqWfy0QyJiJoZeMZGIc7cHHf.svg) no-repeat center;
        width: 24px;
        height: 24px;
        background-size: cover;
        cursor: pointer;
        margin-left: 16px;
  
        &:hover {
          background: url(https://assets.jiker.com/_for_common_project/2024/1227/admin/WyfQs7jzWZQTcL4bTgbFSfTlMDmaIqY2oM09Jize.svg) no-repeat center;
        }
      }
  
      .quote-item-box {
        position: relative;
  
        &.active {
          border: 1px solid rgba(0, 0, 0, 0.85);
        }
  
        position: relative;
        width: 660px;
        min-height: 56px;
        background: #FFFFFF;
        box-shadow: 0px 6px 10px 0px rgba(0, 0, 0, 0.08);
        border-radius: 8px;
        border: 1px solid #fff;
        padding-top: 4px;
  
  
        .input-btn-box {
          display: flex;
          align-items: center;
          justify-content: space-between;
          padding-right: 16px;
          cursor: pointer;
          padding-bottom: 12px;
  
          .input-btn {
            /* position: absolute; */
            right: 12px;
            bottom: 12px;
            width: 32px;
            background: #fafafa;
            height: 32px;
            padding: 4px;
            border-radius: 5px;
            text-align: center;
  
            img {
              width: 24px;
              height: 24px;
            }
  
            &.active {
              background: #627aa3;
  
              &:hover {
                background: #4366A1;
              }
            }
          }
        }
      }
    }
  
    .section-content-box::-webkit-scrollbar {
      width: 8px;
  
    }
  
  
    .section-content-box::-webkit-scrollbar-thumb {
      background-color: #909399;
      border-radius: 6px;
    }
  
    .section-content-box::-webkit-scrollbar-track {
      background-color: transparent;
    }
  
    .page-body {
      padding-top: 0px !important;
      height: calc(100vh - 112px);
      width: 100% !important;
    }
  
    .el-textarea__inner::-webkit-scrollbar {
      width: 8px;
  
    }
  
    @keyframes animationBorder {
  
      0%,
      100% {
        border-color: #eeeeee;
        box-shadow: none;
      }
  
      70% {
        border-color: #627aa3;
        box-shadow: 0px 0px 16px 6px rgba(0, 0, 0, 0.08);
  
      }
    }
  </style>