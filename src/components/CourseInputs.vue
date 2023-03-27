<template>
  <li class="course-inputs">
    <input
      class="input-l"
      type="text"
      v-model.trim="course.name"
      placeholder="Course name"
    />
    <select v-model.number="course.grade">
      <option selected disabled hidden value="0">Grade</option>
      <option value="4">A</option>
      <option value="3.666">A-</option>
      <option value="3.333">B+</option>
      <option value="3">B</option>
      <option value="2.666">B-</option>
      <option value="2.333">C+</option>
      <option value="2">C</option>
      <option value="1.666">C-</option>
      <option value="1.333">D+</option>
      <option value="1">D</option>
    </select>
    <input
      type="number"
      v-model.number="course.credits"
      placeholder="Credits"
      class="input-r"
    />
    <button class="remove-course" @click="removeCourse(course.id)">
      <i class="fa-regular fa-circle-xmark"></i>
    </button>
  </li>
</template>

<script setup>
import { defineEmits } from "vue";
const props = defineProps(["course"]);
const emit = defineEmits(["removeCourse"]);

const removeCourse = () => {
  emit("removeCourse", props.course.id);
};
</script>

<style scoped>
.input-l {
  border-top-left-radius: 8px 8px;
  border-bottom-left-radius: 8px 8px;
  border: 1px solid var(--third);
  margin-right: -1px;
}
.input-r {
  border-top-right-radius: 8px 8px;
  border-bottom-right-radius: 8px 8px;
  border: 1px solid var(--third);
  margin-left: -1px;
}
input {
  padding: 8px;
  margin: 3px 0px;
}
select {
  border: 1px solid var(--third);
  padding: 7px 8px 7px;
}

.remove-course {
  border: 1px solid transparent;
  background-color: transparent;
  color: var(--third);
  cursor: pointer;
  transition: all 0.4s ease;
  margin-left: 6px;
}

.remove-course:hover {
  color: var(--secondary);
}
</style>
