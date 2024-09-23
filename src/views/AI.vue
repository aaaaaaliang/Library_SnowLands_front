<template>
  <Header />

  <div class="main box_center cf mb50">
    <div class="chat-container">
      <div class="chat-box">
        <h3 class="title">与AI对话</h3>
        <div class="messages">
          <div v-for="(msg, index) in messages" :key="index" class="message">
            <span>{{ msg.sender }}: </span>{{ msg.text }}
          </div>
        </div>
        <div class="chat-input-container">
          <input v-model="userMessage" placeholder="输入你的消息..." class="chat-input" />
          <button @click="sendMessage" class="chat-button">发送</button>
        </div>
      </div>
    </div>
  </div>
  <Footer />
</template>

<script>
import { reactive, toRefs } from "vue";
import { sendMessageToAI } from '@/api/user';
import Header from "@/components/common/Header";
import Footer from "@/components/common/Footer";

export default {
  name: "AIChat",
  components: {
    Header,
    Footer,
  },
  setup() {
    const state = reactive({
      messages: [],
      userMessage: "",
    });

    const sendMessage = async () => {
      if (state.userMessage.trim() === "") return;

      console.log("User message before sending:", state.userMessage);

      state.messages.push({ sender: "你", text: state.userMessage });

      try {
        const response = await sendMessageToAI({ query: state.userMessage });
        console.log("AI response:", response);

        if (response.data && response.data.answer) {
          state.messages.push({ sender: "AI", text: response.data.answer });
        } else {
          console.error("No answer received from AI");
        }
      } catch (error) {
        state.messages.push({ sender: "系统", text: "发送失败，请稍后再试。" });
      }

      state.userMessage = "";
    };

    return {
      ...toRefs(state),
      sendMessage,
    };
  },
};

</script>


<style scoped>
.chat-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 80vh;
  padding: 20px;
}

.chat-box {
  width: 100%;
  max-width: 800px; /* 增加框的宽度 */
  background-color: white;
  padding: 30px; /* 增加内边距 */
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.title {
  text-align: center;
  margin-bottom: 30px; /* 增加标题与消息列表的间距 */
  font-size: 20px; /* 增大标题字体 */
}

.messages {
  max-height: 400px; /* 增加消息框的高度 */
  overflow-y: auto;
  margin-bottom: 20px;
  font-size: 15px; /* 增大消息字体 */
}

.message {
  margin: 10px 0; /* 增加消息之间的间距 */
}

.chat-input-container {
  display: flex;
  margin-top: 20px;
}

.chat-input {
  flex: 1;
  padding: 12px; /* 增加输入框内边距 */
  font-size: 15px; /* 增大输入框字体 */
  border-radius: 4px;
  border: 1px solid #ccc;
}

.chat-button {
  padding: 12px 20px; /* 增加按钮大小 */
  margin-left: 10px;
  border-radius: 4px;
  background-color: #f80;
  color: white;
  border: none;
  font-size: 15px; /* 增大按钮字体 */
  cursor: pointer;
}

.chat-button:hover {
  background-color: #d96c00;
}

</style>