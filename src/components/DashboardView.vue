<template>
  <div class="DashboardView">
    <div class="table-container">
      <table>
        <thead>
          <tr>
            <th class="indicator-header">Показатель</th>
            <th class="current-day-header">Текущий день</th>
            <th>Вчера</th>
            <th>Этот день недели</th>
          </tr>
        </thead>
        <tbody>
          <template v-for="(row, index) in tableData" :key="index">
            <tr @click="showChart(row)">
              <td class="indicator">{{ row.indicator }}</td>
              <td class="current-day">{{ row.currentDay }}</td>
              <td :class="row.yesterdayClass"><span class="value">{{ row.yesterday }}</span> <span class="subtext" :class="row.yesterdayClass">{{ row.yesterdayPercentage }}</span></td>
              <td class="weekday" :class="row.weekdayClass"><span class="value">{{ row.weekday }}</span></td>
            </tr>
            <tr v-if="selectedRow === row" class="chart-row">
              <td colspan="4">
                <div class="chart-container">
                  <LineChart :data="chartData" :options="chartOptions" />
                </div>
              </td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import { Line } from 'vue-chartjs';
import { Chart, registerables } from 'chart.js';

Chart.register(...registerables);

export default {
  components: {
    LineChart: Line
  },
  setup() {
    const tableData = ref([
      { indicator: 'Выручка, руб', currentDay: '600 521', yesterday: '480 521', yesterdayPercentage: '4%', yesterdayClass: 'positive', weekday: '4 805 521', weekdayClass: 'positive' },
      { indicator: 'Наличные', currentDay: '100 000', yesterday: '100 000', yesterdayPercentage: '0%', yesterdayClass: 'neutral', weekday: '100 000', weekdayClass: 'neutral' },
      { indicator: 'Безлимитный расчет', currentDay: '100 521', yesterday: '100 521', yesterdayPercentage: '0%', yesterdayClass: 'neutral', weekday: '100 521', weekdayClass: 'neutral' },
      { indicator: 'Кредитные карты', currentDay: '1 300', yesterday: '900', yesterdayPercentage: '44%', yesterdayClass: 'positive', weekday: '1 300', weekdayClass: 'positive' },
      { indicator: 'Средний чек, руб', currentDay: '1 200', yesterday: '800', yesterdayPercentage: '50%', yesterdayClass: 'positive', weekday: '1 200', weekdayClass: 'positive' },
      { indicator: 'Средний гость, руб', currentDay: '1 200', yesterday: '800', yesterdayPercentage: '50%', yesterdayClass: 'positive', weekday: '1 200', weekdayClass: 'positive' },
      { indicator: 'Удаления из чека(после оплаты), руб', currentDay: '1 500', yesterday: '1 600', yesterdayPercentage: '-9%', yesterdayClass: 'negative', weekday: '1 500', weekdayClass: 'neutral' },
      { indicator: 'Удаления из чека(до оплаты), руб', currentDay: '1 300', yesterday: '1 300', yesterdayPercentage: '0%', yesterdayClass: 'neutral', weekday: '1 300', weekdayClass: 'neutral' },
      { indicator: 'Количество чеков', currentDay: '34', yesterday: '36', yesterdayPercentage: '-6%', yesterdayClass: 'negative', weekday: '34', weekdayClass: 'neutral' },
      { indicator: 'Количество гостей', currentDay: '34', yesterday: '32', yesterdayPercentage: '6%', yesterdayClass: 'positive', weekdayClass: 'positive' }
    ]);

    const selectedRow = ref(null);
    const chartData = ref({
      labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul'],
      datasets: [
        {
          label: 'Data',
          backgroundColor: '#41B883',
          data: [0, 10, 5, 2, 20, 30, 45]
        }
      ]
    });

    const chartOptions = ref({
      responsive: true,
      maintainAspectRatio: false
    });

    const showChart = (row) => {
      selectedRow.value = row;
      // Update chartData based on the selected row
      chartData.value = {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul'],
        datasets: [
          {
            label: row.indicator,
            backgroundColor: '#41B883',
            data: [0, 10, 5, 2, 20, 30, 45] // Replace with actual data
          }
        ]
      };
    };

    return {
      tableData,
      selectedRow,
      chartData,
      chartOptions,
      showChart
    };
  }
};
</script>

<style scoped>
.DashboardView {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.chart-container {
  width: 100%;
  max-width: 600px;
  margin-top: 15px; /* Adjusted margin */
}

.table-container {
  width: 100%;
  overflow-x: auto; /* Enable horizontal scrolling for small screens */
}

table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0 4px; /* Adds spacing between rows */
}

th, td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #f4f4f4;
}

.indicator-header, .indicator {
  background-color: #e9ecef; /* Light grey */
}

.current-day-header, .current-day {
  background-color: #cce5ff; /* Light blue */
}

.value {
  color: black; /* Ensure text color is black */
}

.subtext {
  margin-left: 8px;
  font-size: 0.85em;
}

.positive {
  background-color: #d4edda; /* Light green */
}

.positive .subtext {
  color: #28a745; /* Darker green */
}

.negative {
  background-color: #f8d7da; /* Light red */
}

.negative .subtext {
  color: #dc3545; /* Darker red */
}

.neutral {
  background-color: #e9ecef; /* Light grey */
}

.neutral .subtext {
  color: #6c757d; /* Darker grey */
}

.weekday.positive {
  background-color: #d4edda; /* Light green */
}

.weekday.negative {
  background-color: #e9ecef; /* Light grey */
}

.weekday.neutral {
  background-color: #e9ecef; /* Light grey */
}

.weekday .value {
  color: black; /* Ensure text color is black */
}

/* Media queries for responsiveness */
@media (max-width: 768px) {
  .DashboardView {
    padding: 10px;
  }

  th, td {
    font-size: 0.85em;
  }

  .chart-container {
    margin-top: 10px;
  }
}

@media (max-width: 480px) {
  th, td {
    font-size: 0.75em;
    padding: 6px;
  }

  .chart-container {
    margin-top: 8px;
  }
}
</style>
