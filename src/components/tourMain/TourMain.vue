<script setup>
import { ref, onMounted } from "vue";
import { listDeparture, listDestination, question } from "@/api/tourboard";

import VTourSelect from "../common/VTourSelect.vue";
import TourPopular from "./TourPopular.vue";
import TourCheap from "./TourCheap.vue";

const departureList = ref([]);
const destinationList = ref([]);
const depa = ref("");
const dest = ref("");

onMounted(() => {
  getDepartureList();
});

const getDepartureList = () => {
  listDeparture(({ data }) => {
    let option = [];
    for (var i = 0; i < data.length; i++) {
      option.push(data[i].tourboard_departure);
    }
    let uniqueOption = [...new Set(option)];
    console.log(uniqueOption);
    departureList.value = uniqueOption;
  });
};

const onChangeDeparture = (val) => {
  depa.value = val;
  listDestination({ depa: val.value }, ({ data }) => {
    let options = [];
    //   options.push({ text: '도착지', value: '' });
    for (var i = 0; i < data.length; i++) {
      options.push(data[i].tourboard_destination);
    }
    let uniqueOption = [...new Set(options)];
    console.log(uniqueOption);
    destinationList.value = uniqueOption;
    console.log(destinationList);
    //   gugunList.value = options;
  });
};

const onChangeDestination = (val) => {
  dest.value = val;
};
</script>

<template>
  <div class="main-main-box">
    <div class="main-main-box-section01">
      <div class="welcome-msg">
        <p>어디로 떠나볼까요?</p>
      </div>
      <div class="tour-search-box">
        <div class="select-box">
          <VTourSelect :selectOption="departureList" @onKeySelect="onChangeDeparture" />
        </div>
        <div class="select-box">
          <VTourSelect :selectOption="destinationList" @onKeySelect="onChangeDestination" />
        </div>
        <router-link class="search-link" :to="`/tour/toursearchresult/${depa.value}/${dest.value}`"
          >검색</router-link
        >
      </div>
    </div>

    <div id="carouselExampleControls" class="carousel slide w-50" data-bs-ride="carousel">
      <div class="carousel-inner">
        <div class="carousel-item active">
          <img src="src\img\sea.jpg" class="d-block w-100" alt="..." />
        </div>
        <div class="carousel-item">
          <img src="src\img\nature.jpg" class="d-block w-100" alt="..." />
        </div>
        <div class="carousel-item">
          <img src="src\img\kite.jpg" class="d-block w-100" alt="..." />
        </div>
      </div>
      <button
        class="carousel-control-prev"
        type="button"
        data-bs-target="#carouselExampleControls"
        data-bs-slide="prev"
      >
        <span class="carousel-control-prev-icon" aria-hidden="true"></span>
        <span class="visually-hidden">Previous</span>
      </button>
      <button
        class="carousel-control-next"
        type="button"
        data-bs-target="#carouselExampleControls"
        data-bs-slide="next"
      >
        <span class="carousel-control-next-icon" aria-hidden="true"></span>
        <span class="visually-hidden">Next</span>
      </button>
    </div>
  </div>

  <div class="recommend-section"></div>

  <div class="popular-section">
    <TourPopular />
  </div>

  <div class="cheap-section">
    <TourCheap />
  </div>

  <div class="age-section"></div>
</template>

<style scoped>
.main-main-box {
  padding-top: 40px;
  padding-bottom: 40px;
  background-color: #cff0fa;
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}

.welcome-msg > p {
  font-size: 25px;
  font-weight: 550;
}
.select-box {
  width: 400px;
}

.submit-btn {
  background-color: #06233a;
  text-align: center;
}

.search-link {
  text-align: center;
  background-color: #06233a;
  display: block;
  text-decoration: none;
  color: white;
  padding: 10px;
  font-size: 20px;
}

#carouselExampleControls {
  width: 35% !important;
}

.ai-msg {
  font-size: 21px;
  font-weight: 700;
}

.main-main-box-section02 {
  margin-top: 100px;
}

.question-input {
  width: 400px;
}

.question-box {
  display: flex;
  flex-direction: column;
}

.question-btn {
  text-align: center;
  background-color: #06233a;
  display: block;
  text-decoration: none;
  color: white;
  padding: 10px;
  font-size: 20px;
  margin-top: 10px;
  border: none;
}
.question-input {
  outline: none;
  height: 50px;
  padding: 10px;
  border: none;
}

.question-input::placeholder {
  color: rgba(0, 0, 0, 0.4);
}
</style>
