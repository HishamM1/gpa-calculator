<template>
  <div class="title">
    <span style="font-weight: 600">Semester {{ id }}</span>
    <button class="remove-semester" @click="removeSemester">
      <i class="fa-regular fa-circle-xmark"></i>
    </button>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from "vue";
const props = defineProps(["id"]);
const emit = defineEmits(["removeSemester"]);
const removeSemester = () => {
  emit("removeSemester", props.id);
};
</script>

<style scoped>
.title {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  gap: 8px;
  margin-bottom: 10px;
}

.remove-semester {
  font-size: 20px;
  border: 1px solid transparent;
  background-color: transparent;
  transition: all 0.4s ease;
  color: var(--third);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}
.remove-semester:hover {
  color: var(--secondary);
}
</style>
