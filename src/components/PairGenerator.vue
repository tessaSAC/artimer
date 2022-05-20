<script>
export default {
  data: _ => ({
    form: { names: '' },

    namesListProcessed: [],
    namesListShuffled: [],

    tableColumns: [
      {
        title: 'name 1',
        dataKey: 'name1',
        width: '200px',
      },
      {
        title: 'name 2',
        dataKey: 'name2',
        width: '200px',
      },
    ],
    tableHeight: 400,
    tableWidth: 640,
  }),

  computed: {
    // nameListShuffled rotated 1 step
    nameListAssignments() {
      if(!this.namesListShuffled.length) return []

      const assignments = this.namesListShuffled.slice(1)
      assignments.push(this.namesListShuffled[0])

      return assignments
    },

    tableData() {
      const data = []

      for(let i = 0; i < this.namesListShuffled.length; ++i) {
        data.push({
          id: i,
          name1: this.namesListShuffled[i],
          name2: this.nameListAssignments[i]
        })
      }

      return data
    }
  },

  methods: {
    generatePairs() {
      this.processNamesList()
      this.shuffleNamesList()
    },

    processNamesList() {
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
      this.form.names = ''
    },

    shuffleNamesList() {
      this.namesListShuffled = []


      while(this.namesListProcessed.length) {
        if(this.namesListProcessed.length === 1) this.namesListShuffled.push(this.namesListProcessed.pop())

        // Get random idx from remaining unshuffled names
        const idxNameToRemove = Math.floor(Math.random() * this.namesListProcessed.length)

        // Remove randomly chosen name and push to shuffled list
        this.namesListShuffled.push(...this.namesListProcessed.splice(idxNameToRemove, 1))
      }
    },
  }
}
</script>

<template>
<h1>Random pair generator</h1>

<!-- Form -->
<!-- <template v-if="form.names.length"> -->
<template >
  <el-form :model="form" label-width="200px">
    <el-form-item label="Participant names">
      <el-input
        v-model="form.names"
        type="textarea"
        placeholder="Enter comma or newline-separated list of names to pair"
      />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="generatePairs()">
        Generate pairs
      </el-button>
      <el-button>Cancel</el-button>
    </el-form-item>
  </el-form>
</template>

<!-- <template>
  <div style="height: 400px">
        <el-table-v2
          :columns="tableColumns"
          :data="tableData"
          :width="tableWidth"
          :height="tableHeight"
          fixed
        />
  </div>
</template> -->
</template>

