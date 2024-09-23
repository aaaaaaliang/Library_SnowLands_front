<template>
  <div class="topMain">
    <div class="box_center cf">
      <router-link :to="{ name: 'home' }" class="logo fl"
      ><img :src="logo" alt="小说精品屋"
      /></router-link>
      <div class="searchBar fl">
        <div class="search cf">
          <input
              v-model="keyword"
              type="text"
              placeholder="书名、作者、关键字"
              class="s_int"
              v-on:keyup.enter="searchByK"
          />
          <label class="search_btn" id="btnSearch" @click="searchByK()"
          ><i class="icon"></i
          ></label>
        </div>
      </div>

      <div class="bookShelf fr" id="headerUserInfo">
        <span v-if="!token" class="user_link">
          <router-link :to="{ name: 'login' }" class="mr15">登录</router-link>
          <router-link :to="{ name: 'register' }" class="mr15">注册</router-link>
        </span>
        <span v-if="token" class="user_link">
          <router-link :to="{ name: 'userSetup' }" class="mr15">{{ nickName }}</router-link>
          <a @click="logout" href="javascript:void(0)">退出</a>
        </span>
      </div>
    </div>

    <!-- AI 对话框 -->
    <div class="ai-dialog">
      <div class="dialog-messages">
        <div v-for="message in messages" :key="message.id" class="dialog-message">
          <strong v-if="message.sender === 'ai'">AI:</strong>
          <strong v-else>你:</strong>
          <span>{{ message.text }}</span>
        </div>
      </div>
      <input
          v-model="userInput"
          type="text"
          placeholder="输入内容"
          class="dialog-input"
          v-on:keyup.enter="sendMessage"
      />
      <button @click="sendMessage">发送</button>
    </div>
  </div>
</template>

<script>
import logo from "@/assets/images/logo.png";
import { reactive, toRefs } from "vue";
import { useRouter, useRoute } from "vue-router";
import { getToken, getNickName, removeToken, removeNickName, removeUid } from "@/utils/auth";

export default {
  name: "Top",
  setup(props, context) {
    const state = reactive({
      keyword: "",
      nickName: getNickName(),
      token: getToken(),
      userInput: "",
      messages: [],
    });
    const route = useRoute();
    const router = useRouter();
    state.keyword = route.query.key;

    const searchByK = () => {
      router.push({ path: "/bookclass", query: { key: state.keyword } });
      context.emit("eventSerch", state.keyword);
    };

    const logout = () => {
      removeToken();
      removeNickName();
      removeUid();
      state.nickName = "";
      state.token = "";
    };

    const sendMessage = () => {
      if (state.userInput.trim()) {
        state.messages.push({
          id: Date.now(),
          sender: "user",
          text: state.userInput,
        });
        // 模拟 AI 响应
        setTimeout(() => {
          state.messages.push({
            id: Date.now(),
            sender: "ai",
            text: "这是AI的响应：" + state.userInput,
          });
        }, 1000);
        state.userInput = "";
      }
    };

    return {
      ...toRefs(state),
      logo,
      searchByK,
      logout,
      sendMessage,
    };
  },
};
</script>

<style scoped>
.ai-dialog {
  margin-top: 20px;
  padding: 10px;
  background-color: #f4f4f4;
  border-radius: 10px;
}

.dialog-messages {
  max-height: 200px;
  overflow-y: auto;
  margin-bottom: 10px;
}

.dialog-message {
  margin-bottom: 5px;
}

.dialog-input {
  width: calc(100% - 80px);
  margin-right: 10px;
}

button {
  padding: 5px 10px;
  cursor: pointer;
}
</style>
