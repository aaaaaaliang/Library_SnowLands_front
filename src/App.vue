<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App',
  mounted() {
    this.handleGithubOAuth();
  },
  methods: {
    handleGithubOAuth() {
      const urlParams = new URLSearchParams(window.location.search);
      const code = urlParams.get('code');

      if (code) {
        // 将 code 发送到后端进行处理
        fetch('http://localhost:8888/oauth/redirect', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({ code: code }),
        })
            .then(response => response.json())
            .then(data => {
              // 处理后端返回的结果，可以是显示登录成功信息或跳转到其他页面
              if (data.success) {
                this.$message.success('登录成功');
                // 清理 URL 中的 code 参数
                window.history.replaceState({}, document.title, window.location.pathname);
                // 跳转到主页
                this.$router.push('/home');
              } else {
                this.$message.error('登录失败，请重试');
              }
            })
            .catch(error => {
              console.error('Error:', error);
              this.$message.error('请求失败，请重试');
            });
      }
    },
  },
};
</script>
