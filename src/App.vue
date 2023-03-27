<script setup>
import { ref, computed, watch, watchEffect } from "vue";

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

const scale = (n, domainMax, rangeMax) => {
  const rate = n / domainMax;
  return rangeMax * rate;
};

const rotation = computed(() => scale(CGPA.value, 4, 180));
const color = computed(() => {
  if (CGPA.value >= 3) return "#007A3D"; // Green
  if (CGPA.value >= 2) return "#F9D616"; // Yellow
  return "#CE1126"; // Red
});
</script>

<template>
  <!-- Navbar -->
  <nav>
    <h1>GPA Calculator FCDS</h1>
  </nav>
  <!-- Main content -->
  <main>
    <div>
      <section>
        <ul>
          <!-- Semester Card -->
          <li class="semester-card" v-for="semester in semesters" :key="semester.id">
            <ul>
              <div
                style="
                  display: flex;
                  justify-content: flex-start;
                  align-items: center;
                  gap: 8px;
                  margin-bottom: 10px;
                "
              >
                <span style="font-weight: 600">Semester {{ semester.id }}</span>
                <button class="remove-semester" @click="removeSemester(semester.id)">
                  <i class="fa-regular fa-circle-xmark"></i>
                </button>
              </div>
              <!-- Course Inputs -->
              <li
                class="course-inputs"
                v-for="course in semester.courses"
                :key="course.id"
              >
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
                <button
                  class="remove-course"
                  @click="removeCourse(semester.id, course.id)"
                >
                  <i class="fa-regular fa-circle-xmark"></i>
                </button>
              </li>
            </ul>
            <div class="semester-gpa">
              Semester {{ semester.id }} GPA:
              <h4 style="display: inline">{{ semester.gpa }}</h4>
            </div>
            <button class="add-course" @click="addCourse(semester.id)">Add Course</button>
          </li>
        </ul>
      </section>
      <button class="add-semester" @click="addSemester">Add Semester</button>
    </div>
    <!-- Cumulative GPA -->

    <div>
      <svg id="gauge" height="180" width="300">
        <defs>
          <mask id="donut">
            <path
              d="M 0 150
           A 45 45, 0, 0, 1, 300 150
           L 230 150
           A 45 45, 0, 0, 0, 70, 150
           L 0 150"
              fill="white"
              stroke="black"
            />
          </mask>
        </defs>

        <path
          d="M 0 150
           A 45 45, 0, 0, 1, 300 150
           L 230 150
           A 45 45, 0, 0, 0, 70, 150
           L 0 150"
          fill="white"
          stroke="#BBBBBB"
        />

        <g mask="url(#donut)">
          <rect
            x="0"
            y="150"
            height="150"
            width="300"
            :fill="color"
            :transform="`rotate(${rotation} 150 150)`"
          />
        </g>

        <text class="rate" x="150" y="135" text-anchor="middle">
          {{ CGPA }}
        </text>
        <text class="label" x="150" y="180" text-anchor="middle">CGPA</text>
      </svg>
    </div>
  </main>
</template>

<style>
* {
  padding: 0;
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
}

body {
  background-color: #f6f1f1;
}
nav {
  box-shadow: 0px 0px 0px 1px rgba(0, 0, 0, 0.07);
  padding: 9px 60px;
}

nav h1 {
  color: #19a7ce;
  font-weight: 600;
  font-size: xx-large;
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
  border: 1px solid #19a7ce;
  padding: 10px;
  font-size: medium;
  background-color: transparent;
  color: #19a7ce;
  transition: all 0.4s ease;
  cursor: pointer;
}

.add-semester:hover {
  border: 1px solid #146c94;
  color: #146c94;
  box-shadow: 0px 15px 20px rgba(0, 0, 0, 0.1);
}

.add-course {
  border-radius: 10px;
  border: 1px solid #19a7ce;
  padding: 5px;
  font-size: small;
  background-color: transparent;
  color: #19a7ce;
  transition: all 0.4s ease;
  cursor: pointer;
  margin-top: 5px;
}

.add-course:hover {
  border: 1px solid #146c94;
  color: #146c94;
  /* box-shadow: 0px 15px 20px rgba(0, 0, 0, 0.1); */
}
input:focus,
select:focus {
  outline: none;
  border: 1px solid #146c94;
}
.remove-semester {
  font-size: 20px;
  border: 1px solid transparent;
  background-color: transparent;
  transition: all 0.4s ease;
  color: #19a7ce;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}
.remove-semester:hover {
  color: #146c94;
}
.input-l {
  border-top-left-radius: 8px 8px;
  border-bottom-left-radius: 8px 8px;
  border: 1px solid #19a7ce;
  margin-right: -1px;
}
.input-r {
  border-top-right-radius: 8px 8px;
  border-bottom-right-radius: 8px 8px;
  border: 1px solid #19a7ce;
  margin-left: -1px;
}
input {
  padding: 8px;
  margin: 3px 0px;
}
select {
  border: 1px solid #19a7ce;
  padding: 7px 8px 7px;
}

.semester-card:not(:last-child) {
  margin-bottom: 20px;
}

.remove-course {
  border: 1px solid transparent;
  background-color: transparent;
  color: #19a7ce;
  cursor: pointer;
  transition: all 0.4s ease;
  margin-left: 6px;
}

.remove-course:hover {
  color: #146c94;
}

.semester-gpa {
  margin: 6px 0px;
}
</style>
