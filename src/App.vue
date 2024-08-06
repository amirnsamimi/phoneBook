<script setup>
import { computed, onMounted, ref, watch } from "vue";
const STATES = {
  IDLE: "initial",
  PEND: "loading",
  SUCC: "success",
  EMPT: "empty",
};
const useFetch = async (url) => {
  const response = await fetch(url);
  return await response.json();
};

const state = ref(STATES.IDLE);
const users = ref([]);
const getUsers = async (limit = 10) => {
  users.value = [];
  state.value = STATES.PEND;
  const data = await useFetch(
    `https://dummyjson.com/users?limit=${limit}&skip=${limit - 10}`
  );

  if (data.users && data.users.length) {
    users.value = data.users;
    state.value = STATES.SUCC;
  } else if (data.users) {
    state.value = STATES.EMPT;
  }
};

onMounted(() => {
  getUsers();
});

// const repeatedUsers = computed(()=>{
//  return users.value.filter((i)=> i.last_name === "کیمیایی")
// })

// const usersInYazd = computed(()=>{
//   return users.value.filter(i=>i.province === "یزد")
// })

// const allProvinces = computed(()=>{
//   const provinces = []
//   users.value.forEach((i)=>{
//    if(!provinces.includes(i.province)){
//     provinces.push(i.province)
//    }
//   })
//   return provinces
// })

// const groupByProvince = computed(()=>{

//   const objProvince = {}
//   allProvinces.value.forEach((i)=>{
//     objProvince[i] = []
//   })
//   users.value.forEach((user)=>{
//     objProvince[user.province].push(user)
//   })

// })
</script>
<template>
  <main>
    <section>
      <div class="phoneBookOwner">
        <h1>Amir</h1>
        <img src="" alt="" />
        <h2>Amir Samimi</h2>
        <p>amirnsamimi@gmail.com</p>
        <div>
          <div>
            <svg><use /></svg> date
          </div>
          <div>
            <svg><use /></svg> phone
          </div>
          <div>
            <svg><use /></svg> address
          </div>
        </div>
      </div>
      <div class="controllers">
        <h2>Address Book</h2>
        <div class="controll-buttons"><button class="controllButton">filter</button><button class="controllButton">search</button><button class="addButton">new contact</button></div>
      </div>
      <div class="contactList">
        <ul>
          <li v-for="user in users">
            <img src="" alt="" />
            <div>
              <h2>{{ user.firstName }} {{ user.lastName }}</h2>
              <p>{{ user.company?.title }}</p>
            </div>
            <div>{{ user.phoneNumber }}</div>
            <div>{{ user.email }}</div>
          </li>
        </ul>
        <div>
        <img src="" alt="" />
        <h2>Amir Samimi</h2>
        <p>best friend</p>
        <div><button></button><button></button><button></button></div>
        <div>
          <div>
            <h2>Phone Number</h2>
            <h3>09124971667</h3>
          </div>
          <div>
            <img src="" alt="" />
          </div>
        </div>
        <div>
          <div>
            <h2>Phone Number</h2>
            <h3>09124971667</h3>
          </div>
          <div>
            <img src="" alt="" />
          </div>
        </div>
        <div>
          <div>
            <h2>Phone Number</h2>
            <h3>09124971667</h3>
          </div>
          <div>
            <img src="" alt="" />
          </div>
        </div>
      </div>
      </div>
     
      <div v-if="state === STATES.PEND">در حال دریافت اطلاعات ...</div>
      <div v-else-if="state === STATES.EMPT">هیچ کاربری پیدا نشد</div>
     
    </section>
  </main>
</template>
<style>
ul{
  list-style-type: none;

}
section{
  display: grid;
  width: 100%;
  grid-template: repeat(12, 1fr) / repeat(12, 1fr);
}

.phoneBookOwner{
  grid-column:1 / 3 span;
  grid-row: 1 / 12 span;
  border-right: #9A9A9A 2px solid;
}

.controllers{
  grid-column: 4 / 12 span ;
  grid-row: 1 / 1 span;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-inline: 2rem;
  border-bottom: #9A9A9A 2px solid;
}
.controll-buttons{
  display: flex;
  gap: 1rem;
}
.addButton{
  background-color: #55C875;
  border: none;
  width: max-content;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
}

.controllButton{
  background-color: #D9D9D9;
  border: none;
  width: max-content;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
}

.contactList{
  grid-column: 4 / 12 span;
  grid-row: 2 / 11 span;
  background-color: blue;
  display: flex;
}


</style>
