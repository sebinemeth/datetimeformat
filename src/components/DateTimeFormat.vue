<template>
  <v-container>
    <v-row>
      <v-col cols="12" md="6">
        <v-autocomplete
          :items="locales.all"
          v-model="displayLocale"
          :item-text="(option) => `${option.name} (${option.tag})`"
          item-value="tag"
        ></v-autocomplete>

        <v-radio-group v-model="formatFn" :disabled="hasOptions">
          <v-radio
            v-for="(option, n) in formatFnOptions"
            :key="n"
            :label="`Date.prototype.${option}()`"
            :value="n"
          ></v-radio>
        </v-radio-group>

        <template v-for="(options, key) in dateTimeFormatOptions">
          <v-autocomplete
            :key="key"
            :label="key"
            :items="options"
            v-model="dateTimeFormatRaw[key]"
            clearable
          ></v-autocomplete>
        </template>
      </v-col>
      <v-col cols="12" md="6">
        <v-card class="mb-3">
          <v-card-title>
            {{ formattedDate }}
          </v-card-title>
        </v-card>
        <v-card class="mb-3">
          <v-card-text>
            <pre>Date.prototype.{{ formatFnOptions[formatFn] }}(loc, opt)</pre>
          </v-card-text>
        </v-card>
        <v-card>
          <v-card-text>
            <pre>{{ dateTimeFormat }}</pre>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Component, Vue, Watch } from "vue-property-decorator";
import locales from "locale-codes";

@Component
export default class DateTimeFormat extends Vue {
  locales = locales;
  dateTimeFormatOptions = {
    weekday: ["narrow", "short", "long"],
    era: ["narrow", "short", "long"],
    year: ["2-digit", "numeric"],
    month: ["2-digit", "numeric", "narrow", "short", "long"],
    day: ["2-digit", "numeric"],
    hour: ["2-digit", "numeric"],
    minute: ["2-digit", "numeric"],
    second: ["2-digit", "numeric"],
    timeZoneName: ["short", "long"],
  };
  formatFnOptions = [
    "toLocaleString",
    "toLocaleDateString",
    "toLocaleTimeString",
  ];
  now = new Date();
  interval = null as number | null;

  displayLocale = "en";
  formatFn = 0;
  dateTimeFormatRaw = {};

  get hasOptions(): boolean {
    return JSON.stringify(this.dateTimeFormat) !== "{}";
  }

  get dateTimeFormat(): Record<string, string> {
    let df = {};
    for (const [key, opt] of Object.entries(this.dateTimeFormatRaw))
      if (opt) df = { ...df, ...{ [key]: opt } };
    return df;
  }

  get formattedDate(): string {
    // eslint-disable-next-line @typescript-eslint/ban-ts-comment
    //@ts-ignore
    return this.now[this.formatFnOptions[this.formatFn]](
      this.displayLocale,
      this.dateTimeFormat
    );
  }

  mounted(): void {
    this.interval = window.setInterval(() => (this.now = new Date()), 1000);
  }

  beforeDestroy(): void {
    window.clearInterval(this.interval as number);
  }

  @Watch("dateTimeFormat")
  onDateTimeFormatChange(): void {
    if (this.hasOptions) this.formatFn = 0;
  }
}
</script>
