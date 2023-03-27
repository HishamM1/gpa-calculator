<template>
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
      <text class="cgba" x="150" y="180" text-anchor="middle">CGPA</text>
    </svg>
  </div>
</template>

<script setup>
import { computed } from "vue";
const props = defineProps(["CGPA"]);
const scale = (n, domainMax, rangeMax) => {
  const rate = n / domainMax;
  return rangeMax * rate;
};
const rotation = computed(() => scale(props.CGPA, 4, 180));
const color = computed(() => {
  if (props.CGPA >= 3) return "#AA4465";
  if (props.CGPA >= 2) return "#023C40";
  return "#CE1126"; // Red
});
</script>
