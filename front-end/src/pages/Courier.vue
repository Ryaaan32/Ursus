<template>
  <el-container class="dashboard">
    <el-header class="header">
      <CourierHeaderBar :userInfo="userInfo" :type="'courier'"></CourierHeaderBar>
    </el-header>
    <el-container class="content">
      <el-aside width="200px">
        <CourierAsidePanel :userInfo="userInfo"></CourierAsidePanel>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
/* eslint-disable */
import CourierHeaderBar from "../components/CourierHeaderBar";
import CourierAsidePanel from "../components/CourierAsidePanel";
export default {
  name: "Courier",
  components: { CourierHeaderBar, CourierAsidePanel },
  data() {
    return {
      id: "",
      location: {
        accuracy: "",
        longitude: "",
        latitude: ""
      },
      userInfo: {
        id: "",
        telephone: "",
        bcAddress: "",
        type: ""
      }
    };
  },
  mounted() {
    this.id = this.$route.params.uid;
    fetch("/user?id=" + this.id).then(res => {
      if (res.ok) {
        res.json().then(res => {
          console.log(res);
          this.userInfo.id = res.id;
          this.userInfo.telephone = res.telephone;
          this.userInfo.bcAddress = res.bcAddress;
          this.userInfo.type = res.type;
        });
      } else {
        console.log("Request error");
      }
    });
    //定位
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(position => {
        // eslint-disable-next-line
        console.log(position);
        this.location.accuracy = position.coords.accuracy;
        this.location.latitude = position.coords.latitude;
        this.location.longitude = position.coords.longitude;
        fetch("/location", {
          headers: new Headers({ "Content-Type": "application/json" }),
          method: "PUT",
          body: JSON.stringify({
            uid: this.id,
            latitude: this.location.latitude,
            longitude: this.location.longitude,
            accuracy: this.location.accuracy
          })
        }).then(res => {
          if (res.ok) {
            res.json().then(res => {
              console.log(res);
            });
          } else {
            console.log("request error");
          }
        });
      });
    }
  }
};
</script>

<style scoped>
.el-main {
  overflow: scroll;
  height: 100%;
}
.dashboard {
  width: 100%;
  height: 100%;
}
.header {
  height: 7vh;
}
.content {
  height: 92vh;
}
</style>
