<script setup>
import { computed, onMounted, ref, watch } from "vue";
import OwnerInfoComponent from "./components/ownerInfo.component.vue";
import UserComponent from "./components/user.component.vue";
import UserDetailsComponent from "./components/userDetails.component.vue";
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

const fullSerach = ref(false);
const selectedContact = ref({});
const searchState = ref(false);
const fullText = ref("");
const state = ref(STATES.IDLE);
const users = ref([]);
const activePage = ref(1);
const filter = ref(false);
const pagination = ref([]);
const limit = ref(10);
const limitPerPage = ref(10);
const startPage = ref(0);
const stepper = ref(8);
const endPage = ref(8);
const female = ref(false);
const getUsers = async (url) => {
  users.value = [];
  state.value = STATES.PEND;
  const data = await useFetch(
    url
      ? url
      : `https://dummyjson.com/users?limit=${limitPerPage.value}&skip=${
          limit.value - 10
        }`
  );
  if (data.users && data.users.length) {
    pagination.value = new Array(Math.ceil(data.total / limitPerPage.value))
      .fill()
      .map((_, i) => i + 1);
    users.value = data.users;
    state.value = STATES.SUCC;
  } else if (data.users) {
    state.value = STATES.EMPT;
  }
};

const getNewUsers = (page) => {
  limit.value = page * limitPerPage.value;
  activePage.value = page;
  getUsers();
};

const getNewUserHandler = (direction) => {
  if (direction === "prev") {
    activePage.value !== 1 && activePage.value--;
    limit.value = activePage.value * limitPerPage.value;
  } else {
    activePage.value < pagination.value.length && activePage.value++;
    limit.value = activePage.value * limitPerPage.value;
  }
  getUsers();
};

onMounted(() => {
  getUsers();
});

const paginationLeftLogic = computed(() => {
  const data = [...pagination.value];
  if (activePage.value === endPage.value) {
    startPage.value += 4;
    endPage.value += 4;
  } else if (
    activePage.value !== 1 &&
    activePage.value === startPage.value + 1
  ) {
    startPage.value -= 4;
    endPage.value -= 4;
  }
  return data.splice(startPage.value, stepper.value);
});

const searchUser = computed(() => {
  if (!fullSerach.value && !female.value) {
    return users.value.filter(
      (user) =>
        user.firstName.toLowerCase().includes(fullText.value.toLowerCase()) ||
        user.lastName.toLowerCase().includes(fullText.value.toLowerCase()) ||
        user.phone
          .replace(" ", "")
          .replaceAll("-", "")
          .includes(fullText.value.toLowerCase())
    );
  } else {
    return users.value.filter((user) => user.gender === "female");
  }
});

const femaleHandler = () => {
  female.value = !female.value;
};

watch(fullText, () => {
  if (fullSerach) {
    getUsers(`https://dummyjson.com/users/search?q=${fullText.value}`);
  }
});
</script>
<template>
  <main>
    <section>
      <OwnerInfoComponent firstName="Amir" lastName="Samimi"  /> 
      <div class="controllers">
        <h2>Address Book</h2>
        <div class="controll-buttons">
          <button
            @click.prevent="() => (filter = !filter)"
            class="controllButton"
          >
            <svg height="20" width="20">
              <use xlink:href="@/assets/sprite.svg#filter" />
            </svg>
            <label
              v-if="filter"
              class="checkboxLabel"
              @click.prevent.stop="femaleHandler"
              ><input type="checkbox" />
              <div class="faker"></div>
              only Females</label
            ></button
          ><button
            @click.prevent="() => (searchState = !searchState)"
            class="searchlButton"
          >
            <svg height="24" width="24">
              <use xlink:href="@/assets/sprite.svg#search" />
            </svg>

            <input
              type="text"
              @click.prevent.stop
              v-model="fullText"
              :style="`${searchState && 'width:200px'}`"
              v-if="searchState"
              placeholder="search for contact ..."
            />
            <label
              v-if="searchState"
              class="checkboxLabel"
              @click.stop="() => (fullSerach = !fullSerach)"
              ><input type="checkbox" />
              <div class="faker"></div>
              Full Search</label
            >
          </button>
          <button class="addButton">
            <svg height="24" width="24">
              <use xlink:href="./assets/sprite.svg#add" />
            </svg>
            new contact
          </button>
        </div>
      </div>
      <div class="contactList">
        <div class="contactContainer">
          <div class="pagination">
            <div class="paginationButtons">
              <div v-for="page in paginationLeftLogic">
                <button
                  @click.prevent="getNewUsers(page)"
                  :class="`${
                    activePage === page ? 'paginationActive' : 'paginationBtn'
                  }`"
                >
                  {{ page }}
                </button>
              </div>
            </div>
            <div>
              <button @click.prevent="getNewUserHandler('prev')" class="icon">
                <svg height="24" width="24">
                  <use xlink:href="@/assets/sprite.svg#prev" />
                </svg>
              </button>
              <button @click.prevent="getNewUserHandler('next')" class="icon">
                <svg height="24" width="24">
                  <use xlink:href="@/assets/sprite.svg#next" />
                </svg>
              </button>
            </div>
          </div>
          <ul class="userItems">
            <li
              :class="`${!searchUser && 'skeleton'} userItem`"
              v-for="user in searchUser"
            >
              <UserComponent :user @select="()=>selectedContact = user"/>
            </li>
          </ul>
        </div>
        <UserDetailsComponent :selectedContact />
      </div>
    </section>
  </main>
</template>
<style>
.skeleton {
  background-color: #eee;
  border-radius: 4px;
}

.skeleton-text {
  height: 16px;
  width: 100%;
  margin-bottom: 8px;
}

.skeleton-img {
  height: 100px;
  width: 100%;
}

.skeleton-avatar {
  height: 50px;
  width: 50px;
  border-radius: 50%;
}

@keyframes shimmer {
  0% {
    background-position: -500px 0;
  }
  100% {
    background-position: 500px 0;
  }
}

div .skeleton {
  background: linear-gradient(to right, #eee 8%, #ddd 18%, #eee 33%);
  background-size: 1000px 100%;
  animation: shimmer 1.5s infinite linear;
}

.designer {
  display: flex;
  justify-content: center;
}
.dataSeries {
  display: grid;
  gap: 1rem;
}

.buttonSeries {
  background-color: #d9d9d9;
  padding: 0.25rem;
  border-radius: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 2rem;
}
.buttonSeries button {
  background-color: transparent;
  color: #7c7c7c;
  padding: 0.5rem 1.25rem;
  border: none;
  border-radius: 1rem;
}

.buttonSeries button.active {
  background-color: #55c875;
  color: black;
  padding: 0.5rem 1.25rem;
  border: none;
  border-radius: 1rem;
}

.selectedContact {
  border: 1px solid black;
  border-radius: 1rem;
  margin: 4rem 1rem;
  padding: 1rem;
}
input[type="text"] {
  border: 1px solid black;
  border-radius: 0.5rem;
  height: 1.75rem;
  padding: 0.5rem;
  outline: none;
  width: 0px;
  transition: all 1s;
}

.checkboxLabel {
  cursor: pointer;
  display: flex;
  align-items: center;
  height: 100%;
  gap: 0.5rem;
  font-size: 12px;
}

input[type="checkbox"] {
  display: none;
}
input[type="checkbox"] ~ .faker {
  border: 1px solid black;
  width: 20px;
  height: 20px;
  z-index: 10;
  border-radius: 0.25rem;
  background-color: white;
}

input[type="checkbox"]:checked ~ .faker {
  background-color: #55c875;
}

.icon {
  background-color: transparent;
  stroke: black;
  border: none;
  margin-inline: 0.5rem;
  width: 24px;
  height: 24px;
}
button {
  cursor: pointer;
}

.primaryBtn {
  background-color: transparent;
  display: flex;
  justify-content: center;
  align-items: center;
  border: none;
}

ul {
  list-style-type: none;
}
section {
  display: grid;
  width: 100%;
  min-height: 100dvh;
  grid-template: repeat(12, 1fr) / repeat(12, 1fr);
}

.phoneBookOwner {
  grid-column: 1 / 3 span;
  grid-row: 1 / 12 span;
  border-right: #9a9a9a 2px solid;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  position: sticky;
  top: 0;
  max-height: 100vh;
}

.phoneBookOwner h1 {
  font-size: 2rem;
}

.personalInfo {
  display: grid;
  justify-content: center;
  margin-block: 2rem;
  justify-items: center;
}

.personalInfo img {
  margin-bottom: 0.5rem;
  height: 100px;
  width: 100px;
  object-fit: cover;
}
.personalAddInfo {
  display: grid;
  gap: 1rem;
}
.personalAddInfo > div {
  display: flex;
  align-items: center;
  gap: 1rem;
  color: #7c7c7c;
  font-family: "Lato";
  font-weight: 500;
}

.controllers {
  grid-column: 4 / 12 span;
  grid-row: 1 / 1 span;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-inline: 2rem;
  border-bottom: #9a9a9a 2px solid;
  position: sticky;
  top: 0;
  background-color: white;
}
.controll-buttons {
  display: flex;
  gap: 1rem;
}
.addButton {
  background-color: #55c875;
  border: none;
  width: max-content;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  display: flex;
  align-items: center;
  gap: 1rem;
}

.controllButton {
  background-color: #d9d9d9;
  border: none;
  width: max-content;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  display: flex;
  gap: 0.5rem;
}

.searchlButton {
  background-color: #d9d9d9;
  border: none;
  width: max-content;
  display: flex;
  gap: 1rem;

  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
}

.contactList {
  grid-column: 4 / 9 span;
  grid-row: 2 / 11 span;
  padding: 2rem;
  display: flex;
  align-items: flex-start;
}

.pagination {
  display: flex;
  justify-content: space-between;
}
.paginationButtons {
  gap: 1rem;
  display: flex;
}
.contactContainer {
  display: grid;
  gap: 2rem;
  width: 100%;
}

.userItems {
  width: 100%;

  display: grid;
  gap: 2rem;
}
.userInfo {
  display: flex;
  align-items: center;
  gap: 2rem;
  width: 300px;
}

.userPhone {
  text-align: justify;
}
.userItem {
  border: 1px solid #9a9a9a;
  padding: 1rem;
  border-radius: 1rem;
  display: flex;
  justify-content: space-between;
  width: 100%;
  align-items: center;
  gap: 1rem;
}

.paginationActive {
  width: 24px;
  height: 24px;
  background-color: #55c875;
  border: 1px solid black;
  border-radius: 0.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

.paginationBtn {
  width: 24px;
  height: 24px;
  background-color: transparent;
  border: 1px solid black;
  border-radius: 0.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

.userItem img {
  width: 80px;
  height: 80px;
}

.userEmail {
  display: flex;
  align-items: center;
  gap: 1rem;
}

a {
  text-decoration: none;
  color: black;
  cursor: pointer;
}

.infoBtn {
  background-color: white;
  height: 24px;
  width: 24px;
  border: 2px solid black;
  border-radius: 0.5rem;
  font-size: 14px;
}
</style>
