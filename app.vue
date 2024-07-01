<template>
  <div class="p-4 bg-gray-100 max-w-screen">
    <div class="flex items-center justify-between w-[25rem] mb-4">
      <UToggle
        on-icon="i-heroicons-eye"
        off-icon="i-heroicons-eye-slash"
        :model-value="showScores"
        @change="showScores = !showScores"
      />

      <UButton
        @click="resetGame"
        label="Restart game"
        icon="i-heroicons-arrow-path"
        size="2xs"
        color="red"
      />
    </div>

    <table class="mb-4 w-[25rem]">
      <thead>
        <tr>
          <th
            v-for="col in 11"
            :key="'header-col-' + (col + 1)"
            class="text-center p-4 pb-0"
          ></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(row, rowIndex) in 4"
          :key="'row-' + row"
          :class="
            row === 1
              ? 'bg-red-100 border-red-500'
              : row === 2
              ? 'bg-yellow-100 border-yellow-500'
              : row === 3
              ? 'bg-green-100 border-green-500'
              : 'bg-blue-100 border-blue-500'
          "
          class="border-[1px]"
        >
          <td v-for="col in 12" :key="'col-' + col">
            <div
              class="w-4 h-4 p-4 flex items-center relative bg-white rounded-full"
            >
              <UCheckbox
                @change="onSaveGameState"
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
                :disabled="
                  col === 12
                    ? checkboxStates[rowIndex]
                        .slice(0, 11)
                        .filter((state) => state).length < 5
                    : checkboxStates[rowIndex][11]
                "
                class="absolute w-4 h-4 left-2 top-1.5 scale-125 opacity-0"
                :class="{
                  'opacity-100': checkboxStates[rowIndex][col - 1],
                }"
              />
              <div
                class="font-bold text-center"
                :class="
                  row === 1
                    ? 'text-red-500'
                    : row === 2
                    ? 'text-yellow-500'
                    : row === 3
                    ? 'text-green-500'
                    : 'text-blue-500'
                "
              >
                <div
                  v-if="col !== 12"
                  class="absolute left-2.5 top-1 text-center mx-auto block z-10 pointer-events-none"
                  :class="{
                    'opacity-0': checkboxStates[rowIndex][col - 1],
                  }"
                >
                  {{ rowIndex < 2 ? col + 1 : 13 - col }}
                </div>
                <UIcon
                  v-if="col === 12"
                  name="i-heroicons-lock-closed"
                  size="xs"
                  class="absolute left-2 top-2 text-center mx-auto block z-20 pointer-events-none"
                  :class="{
                    'opacity-0': checkboxStates[rowIndex][col - 1],
                    'opacity-20':
                      checkboxStates[rowIndex]
                        .slice(0, 11)
                        .filter((state) => state).length < 5,
                  }"
                />
              </div>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <h2 class="font-semibold">Elke mislukte worp -5</h2>
    <table>
      <tbody>
        <tr>
          <td v-for="n in 4" :key="'checkbox-' + n">
            <div class="block text-center p-2 pb-0 scale-125">
              <UCheckbox
                @change="onSaveGameState"
                color="gray"
                v-model="grayCheckboxStates[n - 1]"
              />
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <div
      class="flex items-center gap-4 mt-4 w-[25rem]"
      :class="{
        'blur-sm': !showScores,
      }"
    >
      <div
        class="border-red-500 bg-white border-4 w-10 h-10 grid place-items-center rounded-lg"
      >
        <p class="text-red-500 font-bold">
          {{ scoreForFirstRow ?? 0 }}
        </p>
      </div>

      <div>+</div>

      <div
        class="border-yellow-500 bg-white border-4 w-10 h-10 grid place-items-center rounded-lg"
      >
        <p class="text-yellow-500 font-bold">
          {{ scoreForSecondRow ?? 0 }}
        </p>
      </div>

      <div>+</div>

      <div
        class="border-green-500 bg-white border-4 w-10 h-10 grid place-items-center rounded-lg"
      >
        <p class="text-green-500 font-bold">
          {{ scoreForThirdRow ?? 0 }}
        </p>
      </div>

      <div>+</div>

      <div
        class="border-blue-500 bg-white border-4 w-10 h-10 grid place-items-center rounded-lg"
      >
        <p class="text-blue-500 font-bold">
          {{ scoreForFourthRow ?? 0 }}
        </p>
      </div>

      <div>-</div>

      <div
        class="border-gray-500 bg-white border-4 w-10 h-10 grid place-items-center rounded-lg"
      >
        <p class="text-gray-500 font-bold">{{ deductedScore }}</p>
      </div>

      <div>=</div>

      <div
        class="bg-white border-4 border-gray-900 w-10 h-10 grid place-items-center rounded-lg"
      >
        <p class="text-gray-900 font-bold">{{ totalScore }}</p>
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

const showScores = ref(false);

const handleBeforeUnload = (event: BeforeUnloadEvent) => {
  event.preventDefault();
  event.returnValue = '';
};

const resetGame = () => {
  checkboxStates.value = Array(4)
    .fill(null)
    .map(() => Array(12).fill(false));
  grayCheckboxStates.value = Array(4).fill(false);
  showScores.value = false;

  localStorage.removeItem('checkboxStates');
  localStorage.removeItem('grayCheckboxStates');
};

function onSaveGameState() {
  localStorage.setItem(
    'checkboxStates',
    JSON.stringify(checkboxStates.value)
  );
  localStorage.setItem(
    'grayCheckboxStates',
    JSON.stringify(grayCheckboxStates.value)
  );
}

onMounted(() => {
  const savedCheckboxStates = localStorage.getItem('checkboxStates');
  if (savedCheckboxStates) {
    checkboxStates.value = JSON.parse(savedCheckboxStates);
  }

  const savedGrayCheckboxStates = localStorage.getItem(
    'grayCheckboxStates'
  );
  if (savedGrayCheckboxStates) {
    grayCheckboxStates.value = JSON.parse(savedGrayCheckboxStates);
  }
});
</script>
