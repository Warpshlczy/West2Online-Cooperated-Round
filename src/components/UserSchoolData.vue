<template>
  <div>
    <div class="head">
      <h1>{{ name }}</h1>
      <img :src="require(`../assets/${name}.jpg`)" />
    </div>
    <p>{{ info }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "",
      info: "",
    };
  },
  mounted() {
    this.$axios({
      url: "/student/querySchool",
      params: {
        usercode: this.$route.params.id,
      },
    }).then(
      (res) => {
        this.name = JSON.parse(JSON.stringify(res.data)).schoolname;
        this.info = JSON.parse(JSON.stringify(res.data)).introd;
      },
      (error) => {
        alert("加载学校信息失败");
        return error;
      }
    );
  },
};
</script>

<style scoped>
.head {
  font-size: 22px;
  text-align: center;
}
p {
  margin: 2% 3%;
  font-size: 20px;
}
img {
  width: 500px;
  height: 300px;
  border: 2px solid black;
}
</style>