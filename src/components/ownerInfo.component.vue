
<script setup>

defineProps({
    firstName:{
        type: String,
        default: "N/A"
    },
    lastName:{
        type: String,
        default: "N/A"
    }
})


</script>
<template>
<div class="phoneBookOwner">
    <div>
      <h1>Amir</h1>
      <div class="personalInfo">
        <img src="@/assets/boy.svg" alt="profile" />
        <h2>{{ firstName }} {{ lastName }}</h2>
        <p>amirnsamimi@gmail.com</p>
      </div>
      <div class="personalAddInfo">
        <div>
          <svg height="24" width="24">
            <use xlink:href="@/assets/sprite.svg#calendar" />
          </svg>
          08-04-1995
        </div>
        <div>
          <svg height="24" width="24">
            <use xlink:href="@/assets/sprite.svg#phone" />
          </svg>
          +98 912 4971667
        </div>
        <div>
          <svg height="24" width="24">
            <use xlink:href="@/assets/sprite.svg#location" />
          </svg>
          Tehran, Iran
        </div>
      </div>
    </div>
    <div class="designer">
      <a
        href="https://www.figma.com/community/file/1155063894617625185"
        alt="designer"
      >
        Designed By: Thien Nguyen
      </a>
    </div>
  </div>

</template>