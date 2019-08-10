<template>
  <div id="app">
    <label>
      <input name="Fruit" type="radio" value checked="checked" @click="changeBtn('date')" />时间
    </label>
    <label>
      <input name="Fruit" type="radio" value @click="changeBtn('type')" />类别
    </label>
    <div>
      <div class="content" v-if="xFlag">
        <div
          class="bigList"
          v-for="(item, index) in list"
          :key="index"
        >
          <div class="title" v-if="filterFlag == 'date'">{{item[0].date}}</div>
          <div class="title" v-if="filterFlag == 'type'">{{item[0].type}}</div>
          <div class="dragContent"
          :data-radius="item[0].type"
          @drop="drop($event)"
          @dragover="allowDrop($event)">
            <div class="smallList" v-for="(ele) in item" :key="ele.id">
              <img
              class="imglist"
                @dragstart="dragStart($event)"
                @drag="dragging($event)"
                draggable="true"
                :id="ele.id"
                :src="ele.imgUrl"
              />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";
import listJson from "@/json/list.json";

export default {
  name: "app",
  components: {
    draggable
  },
  data() {
    return {
      initialList: [],
      list: [],
      filterFlag: "date",
      xFlag: true
    };
  },
  computed: {
    dragOptions() {
      group: this.initialList;
    }
  },
  created() {},
  mounted() {
    localStorage.setItem('listJson', JSON.stringify(listJson))
    this.initialList = listJson;
    this.filterFun();
  },
  methods: {
    dragStart(event) {
      event.dataTransfer.setData("Text", event.target.id);
    },
    dragging(event) {
      // document.getElementById("demo").innerHTML = " p 元素正在拖动";
    },
    allowDrop(event) {
      event.preventDefault();
    },
    drop(event) {
      event.preventDefault();
      var data = event.dataTransfer.getData("Text");
      event.currentTarget.insertBefore(document.getElementById(data).parentElement, data.parentElement)
      let item = this.initialList.find(ele => ele.id == data)
      let itemType = event.target.parentElement.parentElement.dataset.radius || event.target.dataset.radius
      console.log(this.initialList, item, itemType)
      localStorage.setItem('listJson', JSON.stringify(this.initialList))
    },
    filterFun() {
      let list = JSON.parse(localStorage.getItem('listJson'))
      let arr = [];
      let arrList = [];
      list.forEach(ele => {
        if (arr.indexOf(ele[this.filterFlag]) == -1) {
          arr.push(ele[this.filterFlag]);
          arrList.push(
            list.filter(item => item[this.filterFlag] === ele[this.filterFlag])
          );
        }
      });
      this.list = arrList;
      console.log(arrList);
    },
    changeBtn(val) {
      this.xFlag = false;
      let timer = setTimeout(() => {
        this.xFlag = true;
        clearTimeout(timer);
      }, 0)
      this.filterFlag = val;
      this.filterFun();
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.title {
  text-align: left;
  margin-bottom: 20px;
}
.dragContent{ 
  min-height: 250px;
}
.bigList {
  text-align: left;
}
.smallList {
  display: inline-block;
  width: 300px;
  height: 250px;
  margin-right: 20px;
}
img {
  display: inline-block;
  width: 300px;
  height: 250px;
}
</style>
