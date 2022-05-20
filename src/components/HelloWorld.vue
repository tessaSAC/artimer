<script lang="ts">
export default {
  data: _ => ({
    form: {
      names: ''
    },

    namesListProcessed: []
  }),

  methods: {
    generatePairs() {
      // Trim trailing spaces
      // Split name on newlines if they exist
      let names = this.form.names
                           .trim()
                           .split('\n')

      // Loop through names and extract any newline-separated names
      for (let i = 0; i < names.length; ++i) {
        const currentNameContainsComma = names[i].indexOf(',')

        if (currentNameContainsComma >= 0) {
          const namesExtracted = names[i].split(',')
          names[i] = namesExtracted[0]

          if(namesExtracted[1]) names =  [...names, ...namesExtracted.slice(1)]
        }

        // Trim whitespace from name
        names[i] = names[i].trim()
      }

      this.namesListProcessed = names

      console.log(names, typeof names)
    }
  }
}
</script>

<template>
  <h1>Random pair generator</h1>

  <el-form :model="form" label-width="120px">
    <el-form-item label="Names">
      <el-input
        v-model="form.names"
        type="textarea"
        placeholder="Enter comma or new line-separated list of names to pair"
      />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="generatePairs">Generate pairs</el-button>
      <el-button>Cancel</el-button>
    </el-form-item>
  </el-form>
</template>

