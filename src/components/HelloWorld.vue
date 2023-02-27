<template>
  <div class="mt-2 p-1">
    <div class="col-auto">
      <input type="date" v-model="date" />
      <input type="number" v-model="number_day" />
      <button type="button" @click="getData()">Tìm kiếm</button>
      <div class="plan-main">
        <div class="date">
          <div class="list-date" v-if="arrDate && arrDate != null">
            <div class="title-date"><p style="width: 111px">Rooms</p></div>
            <div
              class="e-date"
              v-for="(item, index) in arrDate"
              :key="index"
              :style="{
                backgroundColor: item.background_color,
                color: item.font_color,
              }"
            >
              <p>{{ item.date }}</p>
              <p>{{ item.date1 }}</p>
              <p>{{ item.booking }}</p>
            </div>
          </div>
        </div>
        <div
          class="plan"
          :style="{ width: (number_day + 1) * 49 + 111 + 'px' }"
        >
          <div class="list-room" v-if="rooms && rooms != null">
            <div class="room-info" v-for="(item, index) in rooms" :key="index">
              <span>{{ item.name }} </span> <span>{{ item.room_type }}</span>
            </div>
          </div>
          <div
            class="room-plan"
            :style="{ width: (number_day + 1) * 49 + 'px' }"
            v-if="data && data != null"
          >
            <div
              class="element"
              v-for="(item, index) in data"
              :key="index"
              :style="{
                position: 'absolute',
                top: item.start_y + 'px',
                left: item.start_x + 'px',
                width: item.end_x - item.start_x + 'px',
                height: item.end_y - item.start_y + 'px',
                background: item.color,
                borderRadius:
                  item.start_x == 0
                    ? '0 8px 8px 0'
                    : item.end_x >= max_width
                    ? '8px 0 0 8px'
                    : '8px',
              }"
            >
              <p>{{ item.lable }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onBeforeMount, watch } from "vue";

const date = ref("2023-02-20");

const number_day = ref(30);

const arrDate = ref(null);

const max_width = ref(0);

const data = ref(null);
const rooms = ref(null);

const url = ref("https://stg-api-cpms.hotelaas.com/api/v1/room_plans?__cmps_debug__=true");

// watch(date, () => getData());
// watch(number_day, () => getData());

const getData = () => {
  axios({
    method: "GET",
    url: url.value,
    headers: {
      "portal-id": 1,
    },
    params: {
      date: date.value,
      number_day: number_day.value,
      filter_type: 1,
    },
  })
    .then((res) => {
      console.log(res.data);
      var result = [];
      var roomData = [];
      res.data.data.forEach((element) => {
        console.log(element);
        result.push(...element.plan);
        roomData.push({
          name: element.name,
          room_type: element.room_level,
        });
      });
      data.value = result;
      rooms.value = roomData;
      max_width.value = Math.max(...result.map((o) => o.end_x));
      console.log(max_width);
      arrDate.value = res.data.date;
    })
    .catch((err) => {
      console.log(err);
    });
};

onBeforeMount(() => {
  getData();
});
</script>
<style scoped>
p {
  padding: 0;
  margin: 0;
}
.element {
  border: 1px rgb(114, 114, 114) solid;
  font-size: 12px;
  padding: 0 5px;
  opacity: 0.8;
}
.room-plan {
  /* border: 1px rgb(197, 197, 197) solid; */
  position: relative;
  background-size: 49px 28px;
  background-image: linear-gradient(to right, grey 1px, transparent 1px),
    linear-gradient(to bottom, grey 1px, transparent 1px);
}
.element p {
  max-height: 100%;
}
.list-room {
  width: 110px;
  height: 100%;
}
.plan {
  display: flex;
}
.room-info {
  border: 1px solid rgb(56, 14, 14);
  height: 28px;
  display: flex;
  justify-content: space-around;
}
.date{
  position: sticky;
  top:0;
  background-color: rgb(255, 255, 255);
  z-index: 10;
}
.list-date {
  display: flex;
}
.e-date {
  min-width: 49px;
  max-width: 49px;
  padding: 0 5px;
  font-size: 10px;
  text-align: center;
  border: 1px solid rgb(56, 14, 14);
  box-sizing: border-box;
  font-weight: 500;
}
.title-date {
  width: 110px;
}
.element p {
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2; /* number of lines to show */
  line-clamp: 2;
  -webkit-box-orient: vertical;
}
.plan-main {
  height: 90vh;
  overflow-y: auto;
}
</style>