<template>
  <div class="p-4 bg-gray-100">
    <table class="mb-8">
      <thead>
        <tr>
          <th
            v-for="col in 11"
            :key="'header-col-' + (col + 1)"
            class="text-center p-4 pb-0"
          >
            {{ col + 1 }}
          </th>
          <th>
            <UIcon
              name="i-heroicons-lock-closed"
              size="xs"
              class="block text-center p-4 pb-0"
            />
          </th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(row, rowIndex) in 4"
          :key="'row-' + row"
          :class="
            row === 1
              ? 'bg-red-100'
              : row === 2
              ? 'bg-yellow-100'
              : row === 3
              ? 'bg-green-100'
              : 'bg-blue-100'
          "
        >
          <td v-for="col in 12" :key="'col-' + col">
            <div class="w-4 h-4 p-4 flex items-center">
              <UCheckbox
                v-model="checkboxStates[rowIndex][col - 1]"
                :color="
                  row === 1
                    ? 'red'
                    : row === 2
                    ? 'yellow'
                    : row === 3
                    ? 'green'
                    : 'blue'
                "
              />
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <h2 class="pl-2">Elke mislukte worp -5</h2>
    <table>
      <tbody>
        <tr>
          <td v-for="n in 4" :key="'checkbox-' + n">
            <div class="block text-center p-2 pb-0">
              <UCheckbox
                color="gray"
                v-model="grayCheckboxStates[n - 1]"
              />
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <div class="flex gap-4 mt-8">
      <div class="bg-red-500 w-10 h-10 grid place-items-center">
        <p class="text-white">{{ scoreForFirstRow ?? 0 }}</p>
      </div>

      <div>+</div>

      <div class="bg-yellow-500 w-10 h-10 grid place-items-center">
        <p class="text-white">{{ scoreForSecondRow ?? 0 }}</p>
      </div>

      <div>+</div>

      <div class="bg-green-500 w-10 h-10 grid place-items-center">
        <p class="text-white">{{ scoreForThirdRow ?? 0 }}</p>
      </div>

      <div>+</div>

      <div class="bg-blue-500 w-10 h-10 grid place-items-center">
        <p class="text-white">{{ scoreForFourthRow ?? 0 }}</p>
      </div>

      <div>-</div>

      <div class="bg-gray-500 w-10 h-10 grid place-items-center">
        <p class="text-white">{{ deductedScore }}</p>
      </div>

      <div>=</div>

      <div
        class="bg-white border-2 border-gray-900 w-10 h-10 grid place-items-center"
      >
        <p class="text-gray-900">{{ totalScore }}</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const checkboxStates = ref(
  Array(4)
    .fill(null)
    .map(() => Array(12).fill(false))
);

const scoreMap = {
  1: 1,
  2: 3,
  3: 6,
  4: 10,
  5: 15,
  6: 21,
  7: 28,
  8: 36,
  9: 45,
  10: 55,
  11: 66,
  12: 78,
};

const scoreForFirstRow = computed(() => {
  const count = checkboxStates.value[0].filter(
    (checked) => checked
  ).length;
  return scoreMap[count as keyof typeof scoreMap];
});

const scoreForSecondRow = computed(() => {
  const count = checkboxStates.value[1].filter(
    (checked) => checked
  ).length;
  return scoreMap[count as keyof typeof scoreMap];
});

const scoreForThirdRow = computed(() => {
  const count = checkboxStates.value[2].filter(
    (checked) => checked
  ).length;
  return scoreMap[count as keyof typeof scoreMap];
});

const scoreForFourthRow = computed(() => {
  const count = checkboxStates.value[3].filter(
    (checked) => checked
  ).length;
  return scoreMap[count as keyof typeof scoreMap];
});

const grayCheckboxStates = ref(Array(4).fill(false));
const checkedGrayCheckboxesCount = computed(
  () => grayCheckboxStates.value.filter(Boolean).length
);
const deductedScore = computed(
  () => checkedGrayCheckboxesCount.value * 5
);

const totalScore = computed(() => {
  return (
    (scoreForFirstRow.value || 0) +
    (scoreForSecondRow.value || 0) +
    (scoreForThirdRow.value || 0) +
    (scoreForFourthRow.value || 0) -
    (deductedScore.value || 0)
  );
});
</script>
