<template>
  <div class="semester-gpa">
    Semester {{ id }} GPA:
    <h4>{{ gpa }}</h4>
  </div>
</template>
<script setup>
const props = defineProps(["id", "gpa"]);
</script>

<style scoped>
.semester-gpa {
  margin: 6px 0px;
}
.semester-gpa h4 {
  color: var(--third);
  display: inline;
}
</style>
