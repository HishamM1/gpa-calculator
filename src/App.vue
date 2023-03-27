<script setup>
import { ref, computed, watch, watchEffect } from "vue";
import gpaMeter from "./components/gpaMeter.vue";
import Navbar from "./layout/Navbar.vue";
import Semester from "./components/Semester.vue";

const semesters = ref([]);

if (localStorage.getItem("semesters") != null) {
  semesters.value = JSON.parse(localStorage.getItem("semesters"));
} else {
  semesters.value = [
    {
      id: 1,
      courses: [
        {
          id: 1,
          name: "",
          grade: 0,
          credits: null,
        },
      ],
      gpa: 0,
    },
  ];
}

const calculateGPA = (semester) => {
  let totalCredits = 0;
  let totalGradeCredits = 0;
  for (const course of semester.courses) {
    if (course.credits !== null && course.grade != 0) {
      totalCredits += course.credits;
      totalGradeCredits += course.grade * course.credits;
    }
  }
  return totalCredits > 0 ? (totalGradeCredits / totalCredits).toFixed(3) : 0;
};

watch(semesters.value, () => {
  for (const semester of semesters.value) {
    semester.gpa = calculateGPA(semester);
  }
});

watchEffect(() => {
  localStorage.setItem("semesters", JSON.stringify(semesters.value));
});

const addCourse = (id) => {
  semesters.value
    .filter((semester) => semester.id == id)[0]
    .courses.push({
      id: semesters.value.filter((semester) => semester.id == id)[0].courses.length + 1,
      name: "",
      grade: 0,
      credits: null,
    });
};

const removeCourse = (semesterId, courseId) => {
  semesters.value.filter(
    (semester) => semester.id == semesterId
  )[0].courses = semesters.value
    .filter((semester) => semester.id == semesterId)[0]
    .courses.filter((course) => course.id != courseId);
};

const addSemester = () => {
  semesters.value.push({
    id: semesters.value.length + 1,
    courses: [
      {
        id: 1,
        name: "",
        grade: 0,
        credits: null,
      },
    ],
  });
};

const removeSemester = (id) => {
  semesters.value = semesters.value.filter((semester) => semester.id != id);
  semesters.value.forEach((semester, index) => {
    semester.id = index + 1;
  });
  console.log(semesters.value);
};

const totalCredits = computed(() => {
  let total = 0;
  for (const semester of semesters.value) {
    for (const course of semester.courses) {
      if (course.grade != 0) {
        total += course["credits"];
      } else {
        continue;
      }
    }
  }
  return total;
});

const gradeXhours = computed(() => {
  let total = 0;
  for (const semester of semesters.value) {
    for (const course of semester.courses) {
      if (course.grade != 0) {
        total += course.credits * course.grade;
      } else {
        continue;
      }
    }
  }
  return total;
});

const CGPA = computed(() => {
  return gradeXhours.value / totalCredits.value > 0
    ? (gradeXhours.value / totalCredits.value).toFixed(3)
    : 0;
});
</script>

<template>
  <!-- Navbar -->
  <Navbar />
  <!-- Main content -->
  <main>
    <div>
      <section>
        <ul>
          <!-- Semester Card -->
          <Semester
            v-for="semester in semesters"
            :key="semester.id"
            :semester="semester"
            @add-course="(id) => addCourse(id)"
            @remove-semester="(id) => removeSemester(id)"
            @remove-course="(semesterId, courseId) => removeCourse(semesterId, courseId)"
          />
        </ul>
      </section>
      <button class="add-semester" @click="addSemester">Add Semester</button>
    </div>
    <!-- Cumulative GPA -->
    <gpaMeter :CGPA="CGPA" />
  </main>
</template>

<style>
:root {
  --primary: #edf0da;
  --secondary: #aa4465;
  --third: #023c40;
}
.cgba {
  font-weight: bold;
  font-size: large;
}

* {
  padding: 0;
  margin: 0;
  font-family: "Alata", sans-serif;
}

body {
  background-color: var(--primary);
  color: var(--secondary);
}

ul {
  list-style: none;
  display: inline-block;
  padding: 0;
  margin: 0;
}

main {
  padding: 40px 0px;
  display: flex;
  justify-content: space-around;
  align-items: center;
}

@media (max-width: 768px) {
  main {
    flex-direction: column;
    gap: 40px;
  }

  .course-inputs select {
    width: 90%;
    display: block;
  }
  .input-l,
  select,
  .input-r {
    border-radius: 8px;
  }

  .course-inputs {
    margin-top: 10px;
  }
}

section {
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
  border-radius: 10px;
  padding: 15px;
  max-width: fit-content;
}

.add-semester {
  letter-spacing: 1px;
  margin-top: 20px;
  border-radius: 10px;
  border: 1px solid var(--third);
  padding: 10px;
  font-size: medium;
  background-color: transparent;
  color: var(--third);
  transition: all 0.4s ease;
  cursor: pointer;
}

.add-semester:hover {
  border: 1px solid var(--secondary);
  color: var(--secondary);
  box-shadow: 0px 15px 20px rgba(0, 0, 0, 0.1);
}
</style>
