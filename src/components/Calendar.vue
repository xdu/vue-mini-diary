<template>
  <div>
    <VCalendar
      is-dark
      color="red"
      :from-page="{ month: 12, year: 2020 }"
      :attributes="attrs"
      v-on:dayclick="clicked"
    />
  </div>
</template>

<script>
import VCalendar from "v-calendar/lib/components/calendar.umd";

export default {
  props: {
    datesWithNote: Array,
  },

  watch: {
    datesWithNote: function (val) {
      // We update the data so the component is re-rendered.
      this.attrs = [
        {
          content: "red",
          highlight: true,
          dates: val,
        },
      ];
    },
  },

  components: {
    VCalendar,
  },

  data: function () {
    return {
      attrs: [
        {
          content: "red",
          highlight: true,
          dates: this.datesWithNote,
        },
      ],
    };
  },

  created: function () {
    console.log("Calendar created : " + this.datesWithNote.length);
  },

  updated: function () {
    console.log("Calendar changed : " + this.datesWithNote.length);
  },

  methods: {
    
    hasNote: function (year, month, day) {
        for(let i = 0; i < this.datesWithNote.length; i++) {
            let elem = this.datesWithNote[i]

            if (elem.getYear() + 1900 == year && elem.getMonth() + 1 == month && elem.getDate() == day) {
                return true
            }
        }
        return false
    },

    greet: (page) => {
      alert("Current page : " + page.year + "." + page.month);
    },

    clicked: function (day) {
        if (this.hasNote(day.year, day.month, day.day)) {
            this.$emit('dayclick', day.id)
        }
    }
  },
};
</script>