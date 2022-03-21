<template>
  <div>
    <p class="my-3 text-gray-200 text-center text-white">The Result</p>
    <table class="border-separate border border-slate-400 w-full">
      <thead>
        <tr>
          <th class="border border-slate-300 text-white" v-for="(head, idx) in Object.keys(dataToPresent[0])" :key="idx">{{ head }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(values, idx) in dataToPresent" :key="idx">
          <td class="border border-slate-300 text-center text-white" v-for="(value, idy) in values" :key="idy">{{ value }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: {
    dataToPresent: Array
  }
}
</script>

<style>

</style>