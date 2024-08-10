<script setup>
import { ref } from 'vue';

const buttonSeriesState = ref("contact");
const props = defineProps({
    selectedContact:{
        type:Object,
        default:{}
    }
})
const emits = defineEmits(["close"])

const close = () => {
    emits("close")
}

</script>


<template>
<div
          v-if="Object.entries(selectedContact).length > 0"
          class="selectedContact"
        >
        <button style="background: transparent; border: none; font-size: 16px;" @click="close">
        x
    </button>
          <div class="personalInfo">
            <img
              v-if="selectedContact.gender === 'female'"
              src="@/assets/girl.svg"
              alt="profile"
            />
            <img
              v-if="selectedContact.gender === 'male'"
              src="@/assets/boy.svg"
              alt="profile"
            />
            <h2>
              {{ selectedContact.firstName }} {{ selectedContact.lastName }}
            </h2>
            <p>{{ selectedContact.email }}</p>
          </div>
          <div class="buttonSeries">
            <button
              @click="() => (buttonSeriesState = 'contact')"
              :class="`${buttonSeriesState === 'contact' && 'active'}`"
            >
              Contact</button
            ><button
              @click="() => (buttonSeriesState = 'work')"
              :class="`${buttonSeriesState === 'work' && 'active'}`"
            >
              Work</button
            ><button
              @click="() => (buttonSeriesState = 'details')"
              :class="`${buttonSeriesState === 'details' && 'active'}`"
            >
              Details
            </button>
          </div>
          <div class="dataSeries">
            <div>
              <h2>
                {{
                  buttonSeriesState === "contact"
                    ? "Phone Number"
                    : buttonSeriesState === "work"
                    ? "Company Name"
                    : "University"
                }}
              </h2>
              <h3>
                {{
                  buttonSeriesState === "contact"
                    ? selectedContact.phone
                    : buttonSeriesState === "work"
                    ? selectedContact.company?.name
                    : selectedContact.university
                }}
              </h3>
            </div>

            <div>
              <h2>
                {{
                  buttonSeriesState === "contact"
                    ? "Email Address"
                    : buttonSeriesState === "work"
                    ? "department"
                    : "Birth Date"
                }}
              </h2>
              <h3>
                {{
                  buttonSeriesState === "contact"
                    ? selectedContact.email
                    : buttonSeriesState === "work"
                    ? selectedContact.company?.department
                    : selectedContact.birthDate
                }}
              </h3>
            </div>

            <div>
              <h2>
                {{
                  buttonSeriesState === "contact"
                    ? "age"
                    : buttonSeriesState === "work"
                    ? "address"
                    : "blood group"
                }}
              </h2>
              <h3>
                {{
                  buttonSeriesState === "contact"
                    ? selectedContact.age
                    : buttonSeriesState === "work"
                    ? selectedContact.company?.address?.address
                    : selectedContact.bloodGroup
                }}
              </h3>
            </div>
          </div>
        </div>

</template>