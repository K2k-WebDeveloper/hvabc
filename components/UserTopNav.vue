<script setup lang="ts">
const route = useRoute();
import { ref, computed, watch } from "vue";
import { env } from "~/helpers/env";
import moment from "moment/moment";
import UiPanel from "~/components/parts/ui-panel.vue";
import type { ApiResponse, PaginatedApiResponse } from "~/types";
import { endpoints } from "~/helpers/endpoints";
import { pitLib } from "~/helpers/pitLib";
import { Vue3Lottie } from "vue3-lottie";

definePageMeta({
  layout: "default",
  middleware: "ensure-login",
});

const page = ref<number>(1);
const loadingUserResponse = ref<boolean>(true);
const userResponse = ref();
let sourcedEndpoint = endpoints.friend.index.my;

const searchQuery = ref("");
const showDropdown = ref(false);

const emit = defineEmits(["search"]);

function getData() {
  loadingUserResponse.value = true;
  $fetch<PaginatedApiResponse>(sourcedEndpoint, {
    method: "post",
    headers: pitLib.util.headers(),
    query: { page: page.value },
    server: false,
  }).then((d) => {
    userResponse.value = d;
    loadingUserResponse.value = false;
  });
  console.log(userResponse);
}

onMounted(() => {
  getData();
});

const filteredFriends = computed(() => {
  if (!userResponse.value?.data?.data) return [];
  return userResponse.value.data.data.filter(
    (friend) =>
      friend.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      friend.email.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});

function handleSearch() {
  showDropdown.value = searchQuery.value.length > 0;
  emit("search", searchQuery.value);
}

function selectFriend(friend) {
  searchQuery.value = friend.name;
  showDropdown.value = false;
  emit("search", searchQuery.value);
}
</script>

<template>
  <!-- navigation top-->
  <div class="nav-header bg-white shadow-xs border-0">
    <div class="nav-top">
      <NuxtLink to="/">
        <div class="row">
          <div class="col">
            <img src="/tpl1/images/logo.png" class="img-fluid w-50">
          </div>
        </div>
<!--        <span class="d-inline-block fredoka-font ls-3 fw-600 text-current font-xxl logo-text mb-0">{{env.APP.NAME}} </span>-->
      </NuxtLink>
      <a href="#" class="mob-menu ms-auto me-2 chat-active-btn"
        ><i
          class="feather-message-circle text-grey-900 font-sm btn-round-md bg-greylight"
        ></i
      ></a>
      <a href="" class="mob-menu me-2"
        ><i
          class="feather-video text-grey-900 font-sm btn-round-md bg-greylight"
        ></i
      ></a>
      <a href="#" class="me-2 menu-search-icon mob-menu"
        ><i
          class="feather-search text-grey-900 font-sm btn-round-md bg-greylight"
        ></i
      ></a>
      <button class="nav-menu me-0 ms-2"></button>
    </div>

    <form class="float-left header-search" @submit.prevent>
      <div class="search-form-2 ms-auto">
        <i class="ti-search font-xss"></i>
        <input
          v-model="searchQuery"
          @input="handleSearch"
          @keydown.enter.prevent="handleSearch"
          type="text"
          class="bg-grey-2 border-0 lh-32 pt-2 pb-2 ps-5 pe-3 font-xssss fw-500 rounded-xl w350 theme-dark-bg"
          placeholder="Search  here."
        />

        <!-- <div v-if="showDropdown" class="search-dropdown">
          <div
            v-for="friend in filteredFriends"
            :key="friend._id"
            @click="selectFriend(friend)"
            class="search-result-item"
          >
            {{ friend.name }}
          </div>
        </div> -->
      </div>
    </form>
    <NuxtLink to="/" class="p-2 text-center ms-3 menu-icon center-menu-icon"><img src="/tpl1/images/home.jpg"  class=" font-lg  btn-round-lg theme-dark-bg" :class="{'alert-primary':route.path.includes('veganlog'),'bg-greylight text-grey-500':!route.path.includes('veganlog')}"></img></NuxtLink>
    <NuxtLink to="/group" class="p-2 text-center ms-0 menu-icon center-menu-icon"><img src="/tpl1/images/group.jpg" class=" font-lg  btn-round-lg theme-dark-bg " :class="{'alert-primary':route.path.includes('group'),'bg-greylight text-grey-500':!route.path.includes('group')}"></img></NuxtLink>
    <a href="#" class="p-2 text-center ms-auto menu-icon" id="dropdownMenu3" data-bs-toggle="dropdown" aria-expanded="false"><span class="dot-count bg-warning"></span><img src="/tpl1/images/notification.jpg" class="font-lg  btn-round-lg theme-dark-bg"></img></a>
    <div
      class="dropdown-menu dropdown-menu-end p-4 rounded-3 border-0 shadow-lg"
      aria-labelledby="dropdownMenu3"
    >
      <h4 class="fw-700 font-xss mb-4">Notification</h4>
      <div class="card bg-transparent-card w-100 border-0 ps-5 mb-3">
        <img
          src="/tpl1/images/user-8.png"
          alt="user"
          class="w40 position-absolute left-0"
        />
        <h5 class="font-xsss text-grey-900 mb-1 mt-0 fw-700 d-block">
          Hendrix Stamp
          <span class="text-grey-400 font-xsssss fw-600 float-right mt-1">
            3 min</span
          >
        </h5>
        <h6 class="text-grey-500 fw-500 font-xssss lh-4">
          There are many variations of pass..
        </h6>
      </div>
      <div class="card bg-transparent-card w-100 border-0 ps-5 mb-3">
        <img
          src="/tpl1/images/user-4.png"
          alt="user"
          class="w40 position-absolute left-0"
        />
        <h5 class="font-xsss text-grey-900 mb-1 mt-0 fw-700 d-block">
          Goria Coast
          <span class="text-grey-400 font-xsssss fw-600 float-right mt-1">
            2 min</span
          >
        </h5>
        <h6 class="text-grey-500 fw-500 font-xssss lh-4">
          Mobile Apps UI Designer is require..
        </h6>
      </div>

      <div class="card bg-transparent-card w-100 border-0 ps-5 mb-3">
        <img
          src="/tpl1/images/user-7.png"
          alt="user"
          class="w40 position-absolute left-0"
        />
        <h5 class="font-xsss text-grey-900 mb-1 mt-0 fw-700 d-block">
          Surfiya Zakir
          <span class="text-grey-400 font-xsssss fw-600 float-right mt-1">
            1 min</span
          >
        </h5>
        <h6 class="text-grey-500 fw-500 font-xssss lh-4">
          Mobile Apps UI Designer is require..
        </h6>
      </div>
      <div class="card bg-transparent-card w-100 border-0 ps-5">
        <img
          src="/tpl1/images/user-6.png"
          alt="user"
          class="w40 position-absolute left-0"
        />
        <h5 class="font-xsss text-grey-900 mb-1 mt-0 fw-700 d-block">
          Victor Exrixon
          <span class="text-grey-400 font-xsssss fw-600 float-right mt-1">
            30 sec</span
          >
        </h5>
        <h6 class="text-grey-500 fw-500 font-xssss lh-4">
          Mobile Apps UI Designer is require..
        </h6>
      </div>
    </div>
    <a href="#" class="p-2 text-center ms-3 menu-icon chat-active-btn"
      ><img class="w40 rounded-circle" src="/images/chat.jpeg"
    /></a>
    <div
      class="p-2 text-center ms-3 position-relative dropdown-menu-icon menu-icon cursor-pointer d-none"
    >
      <i
        class="feather-settings animation-spin d-inline-block font-xl text-current"
      ></i>
      <div class="dropdown-menu-settings switchcolor-wrap">
        <h4 class="fw-700 font-sm mb-4">Settings</h4>
        <h6 class="font-xssss text-grey-500 fw-700 mb-3 d-block">
          Choose Color Theme
        </h6>
        <ul>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="red" checked="" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-red"
                style="background-color: #ff3b30"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="green" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-green"
                style="background-color: #4cd964"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input
                type="radio"
                name="color-radio"
                value="blue"
                checked=""
              /><i class="ti-check"></i>
              <span
                class="circle-color bg-blue"
                style="background-color: #132977"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="pink" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-pink"
                style="background-color: #ff2d55"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="yellow" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-yellow"
                style="background-color: #ffcc00"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="orange" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-orange"
                style="background-color: #ff9500"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="gray" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-gray"
                style="background-color: #8e8e93"
              ></span>
            </label>
          </li>

          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="brown" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-brown"
                style="background-color: #d2691e"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="darkgreen" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-darkgreen"
                style="background-color: #228b22"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="deeppink" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-deeppink"
                style="background-color: #ffc0cb"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="cadetblue" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-cadetblue"
                style="background-color: #5f9ea0"
              ></span>
            </label>
          </li>
          <li>
            <label class="item-radio item-content">
              <input type="radio" name="color-radio" value="darkorchid" /><i
                class="ti-check"
              ></i>
              <span
                class="circle-color bg-darkorchid"
                style="background-color: #9932cc"
              ></span>
            </label>
          </li>
        </ul>

        <div class="card bg-transparent-card border-0 d-block mt-3">
          <h4 class="d-inline font-xssss mont-font fw-700">
            Header Background
          </h4>
          <div class="d-inline float-right mt-1">
            <label class="toggle toggle-menu-color"
              ><input type="checkbox" /><span class="toggle-icon"></span
            ></label>
          </div>
        </div>
        <div class="card bg-transparent-card border-0 d-block mt-3">
          <h4 class="d-inline font-xssss mont-font fw-700">Menu Position</h4>
          <div class="d-inline float-right mt-1">
            <label class="toggle toggle-menu"
              ><input type="checkbox" /><span class="toggle-icon"></span
            ></label>
          </div>
        </div>
        <div class="card bg-transparent-card border-0 d-block mt-3">
          <h4 class="d-inline font-xssss mont-font fw-700">Dark Mode</h4>
          <div class="d-inline float-right mt-1">
            <label class="toggle toggle-dark"
              ><input type="checkbox" /><span class="toggle-icon"></span
            ></label>
          </div>
        </div>
      </div>
    </div>

    <NuxtLink to="/me" class="p-0 ms-3 menu-icon"
      ><img src="/tpl1/images/profile-4.png" alt="user" class="w40 mt--1"
    /></NuxtLink>
  </div>
  <!-- navigation top -->
</template>

<style scoped>
.search-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background-color: white;
  border: 1px solid #ddd;
  border-top: none;
  max-height: 200px;
  overflow-y: auto;
  z-index: 1000;
}

.search-result-item {
  padding: 10px;
  cursor: pointer;
}

.search-result-item:hover {
  background-color: #f5f5f5;
}
</style>