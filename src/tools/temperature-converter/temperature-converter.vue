<script setup lang="ts">
import _ from 'lodash';
import {
  convertCelsiusToKelvin,
  convertDelisleToKelvin,
  convertFahrenheitToKelvin,
  convertKelvinToCelsius,
  convertKelvinToDelisle,
  convertKelvinToFahrenheit,
  convertKelvinToNewton,
  convertKelvinToRankine,
  convertKelvinToReaumur,
  convertKelvinToRomer,
  convertNewtonToKelvin,
  convertRankineToKelvin,
  convertReaumurToKelvin,
  convertRomerToKelvin,
} from './temperature-converter.models';

type TemperatureScale = 'kelvin' | 'celsius' | 'fahrenheit' | 'rankine' | 'delisle' | 'newton' | 'reaumur' | 'romer';

const units = reactive<
  Record<
    string | TemperatureScale,
    { title: string; unit: string; ref: number; toKelvin: (v: number) => number; fromKelvin: (v: number) => number; min?: number; max?: number; maxLimit?: boolean }
  >
      >({
        kelvin: {
          title: 'Kelvin',
          unit: 'K',
          ref: 0,
          toKelvin: _.identity,
          fromKelvin: _.identity,
        },
        celsius: {
          title: 'Celsius',
          unit: '°C',
          ref: 0,
          toKelvin: convertCelsiusToKelvin,
          fromKelvin: convertKelvinToCelsius,
        },
        fahrenheit: {
          title: 'Fahrenheit',
          unit: '°F',
          ref: 0,
          toKelvin: convertFahrenheitToKelvin,
          fromKelvin: convertKelvinToFahrenheit,
        },
        rankine: {
          title: 'Rankine',
          unit: '°R',
          ref: 0,
          toKelvin: convertRankineToKelvin,
          fromKelvin: convertKelvinToRankine,
        },
        delisle: {
          title: 'Delisle',
          unit: '°De',
          ref: 0,
          toKelvin: convertDelisleToKelvin,
          fromKelvin: convertKelvinToDelisle,
          maxLimit: true,
        },
        newton: {
          title: 'Newton',
          unit: '°N',
          ref: 0,
          toKelvin: convertNewtonToKelvin,
          fromKelvin: convertKelvinToNewton,
        },
        reaumur: {
          title: 'Réaumur',
          unit: '°Ré',
          ref: 0,
          toKelvin: convertReaumurToKelvin,
          fromKelvin: convertKelvinToReaumur,
        },
        romer: {
          title: 'Rømer',
          unit: '°Rø',
          ref: 0,
          toKelvin: convertRomerToKelvin,
          fromKelvin: convertKelvinToRomer,
        },
      });

function update(key: TemperatureScale) {
  const { ref: value, toKelvin } = units[key];

  let kelvins = toKelvin(value) ?? 0;

  kelvins = kelvins < 0 ? 0 : kelvins;

  _.chain(units)
    .omit(key)
    .forEach(({ fromKelvin }, index) => {
      units[index].ref = Math.floor((fromKelvin(kelvins) ?? 0) * 100) / 100;
    })
    .value();
}

function setupLimit() {
  _.forEach(units, (unit) => {
    if (unit.maxLimit) {
      unit.max = Math.ceil(unit.fromKelvin(0) * 100) / 100;
    }
    else {
      unit.min = Math.floor(unit.fromKelvin(0) * 100) / 100;
    }
  });
}

setupLimit();
update('kelvin');
</script>

<template>
  <div>
    <n-input-group v-for="[key, { title, unit }] in Object.entries(units)" :key="key" mb-3 w-full>
      <n-input-group-label style="width: 100px">
        {{ title }}
      </n-input-group-label>

      <n-input-number
        v-model:value="units[key].ref"
        style="flex: 1"
        :min="units[key].min"
        :max="units[key].max"
        @update:value="() => update(key as TemperatureScale)"
      />

      <n-input-group-label style="width: 50px">
        {{ unit }}
      </n-input-group-label>
    </n-input-group>
  </div>
</template>
