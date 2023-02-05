<template>
  <div class="container">
    <!-- Header -->
    <div class="row">
      <div class="d-flex flex-column align-items-center">
        <div class="col-12 py-5 text-center">
          <h1 class="Roboto-700">Calculating BMI</h1>
          <hr />
        </div>
        <div class="canvas-box">
          <canvas ref="weightChartEl"></canvas>
        </div>
      </div>
    </div>

    <!-- Form -->
    <div class="row g-3">
      <div class="col-12 mb-3">
        <label class="form-label Roboto-400">Gendre : </label>

        <div class="form-check form-check-inline">
          <input
            type="radio"
            class="btn-check"
            id="male"
            value="male"
            v-model="bmi.gendre"
          />
          <label class="btn btn-outline-success" for="male">Male</label>
        </div>
        <div class="form-check form-check-inline">
          <input
            type="radio"
            class="btn-check"
            id="female"
            value="female"
            v-model="bmi.gendre"
          />
          <label class="btn btn-outline-warning" for="female">Female</label>
        </div>
      </div>

      <div class="col-12 mb-3">
        <label for="age" class="form-label">Age</label>
        <input type="text" class="form-control" id="age" v-model.number="bmi.age" />
      </div>

      <div class="col-md-6 mb-3">
        <label for="weight" class="form-label">Weight</label>
        <input
          type="text"
          class="form-control"
          id="weight"
          v-model.number="bmi.weight"
          autocomplete="off"
        />
      </div>

      <div class="col-md-6 mb-3">
        <label for="height" class="form-label">Height</label>
        <input
          type="text"
          class="form-control"
          id="height"
          v-model.number="bmi.height"
          autocomplete="off"
        />
      </div>

      <div class="col-12 d-grid gap-2">
        <button
          type="button"
          class="btn btn-primary "
          @click="calculate()"
          :disabled="disabled"
        >
          Calculate
        </button>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, shallowRef, computed, watch, nextTick } from "vue";
import Chart from "chart.js/auto";

const bmi = ref({
  gendre: null,
  age: null,
  weight: null,
  height: null,
});

const chartData = ref({
  labels: [],
  datasets: [],
});

const datasets = ref({
  label: "BMI",
  data: [],
  tension: 0.5,
  backgroundColor: [],
});

const disabled = ref(false);
const weightChartEl = ref(null);
const weightChart = ref(null);
const result = ref();

const reset = async () => {

  datasets.value.data = [];
  datasets.value.backgroundColor = [];

  chartData.value.labels = [];
  chartData.value.datasets = [];
};

const calculate = async () => {
  if (bmi.value.weight != null && bmi.value.height != null) {
    await reset();

    result.value =
      bmi.value.weight / ((bmi.value.height / 100) * (bmi.value.height / 100));

    switch (true) {
      case result.value < 16:
        chartData.value.labels.push("Underweight");

        datasets.value.data.push(result.value.toFixed(2));
        datasets.value.backgroundColor.push("#DC0000");


        chartData.value.datasets.push(datasets.value);

        break;

      case result.value > 15 && result.value < 26:
        chartData.value.labels.push("Normal");

        datasets.value.data.push(result.value.toFixed(2));
        datasets.value.backgroundColor.push("#379237");

        chartData.value.datasets.push(datasets.value);

        break;

      case result.value > 25 && result.value < 31:
        chartData.value.labels.push("Overweight");

        datasets.value.data.push(result.value.toFixed(2));
        datasets.value.backgroundColor.push("#FFF80A");

        chartData.value.datasets.push(datasets.value);

        break;

      case result.value > 30:
        chartData.value.labels.push("Obesity");

        datasets.value.data.push(result.value.toFixed(2));
        datasets.value.backgroundColor.push("#DC0000");

        chartData.value.datasets.push(datasets.value);

        break;
    }

    await nextTick(() => {
      disabled.value = true;

      if (weightChart.value) {
        weightChart.value.destroy();
      }

      weightChart.value = new Chart(weightChartEl.value.getContext("2d"), {
        type: "line",
        data: {
          labels: chartData.value.labels,
          datasets: chartData.value.datasets,
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
        },
      });

      setTimeout(() => (disabled.value = false), 1200);
    });
  }
};
</script>
<style>
.canvas-box {
  width: 100%;
  max-width: 720px;
  background-color: white;
  padding: 1rem;
  border-radius: 0.5rem;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}
</style>
