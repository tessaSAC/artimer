<script>
export default {
  data: _ => ({
    form: { names: '' },
    isRoundRobin: true,
    namesListCleaned: [],
    namesListProcessed: [],
  }),

  computed: {
    // nameListShuffled rotated 1 step
    nameListAssignments() {
      if(!this.namesListProcessed.length) return []

      let assignments = []

      // If rotating feedback, rotate list by one
      if(this.isRoundRobin) {
        assignments = this.namesListProcessed.slice(1)
        assignments.push(this.namesListProcessed[0])
      }

      // If pairing, no rotation needed
      else assignments = this.namesListProcessed

      return assignments
    },

    isFormVisible() {
      return !this.namesListProcessed.length
    },

    tableColumnALabel() {
      return this.isRoundRobin
               ? 'Feedback giver'
               : 'Partner'
    },

    tableColumnBLabel() {
      return this.isRoundRobin
               ? 'Feedback recipient'
               : 'Partner'
    },

    tableData() {
      const data = []

      if(this.isRoundRobin) {
        for(let i = 0; i < this.namesListProcessed.length; ++i) {
          data.push({
            id: i,
            name1: this.namesListProcessed[i],
            name2: this.nameListAssignments[i]
          })
        }
      } else {
        const midpoint = Math.floor(this.namesListProcessed.length / 2)
        const listPartnersA = this.namesListProcessed.slice(0, midpoint)
        const listPartnersB = this.namesListProcessed.slice(midpoint)

        for(let i = 0; i < listPartnersA.length; ++i) {
          console.log(listPartnersA[i], listPartnersB[i])

          data.push({
            id: i,
            name1: listPartnersA[i],
            name2: i + 1 === listPartnersA.length
                   && listPartnersB[i + 1]
                     ? `${ listPartnersB[i] } & ${ listPartnersB[i + 1] }`
                     : listPartnersB[i]
          })
        }
      }

      return data
    }
  },

  methods: {
    clearData() {
      this.isRoundRobin = true
      this.form.names = ''
      this.namesListProcessed = []
    },

    generatePairs() {
      this.isRoundRobin = false

      this.processNamesList()
      this.shuffleNamesList()
    },

    generateRotation() {
      this.isRoundRobin = true

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

      this.namesListCleaned = names
      this.form.names = ''
    },

    shuffleNamesList() {
      this.namesListProcessed = []


      while(this.namesListCleaned.length) {
        if(this.namesListCleaned.length === 1) this.namesListProcessed.push(this.namesListCleaned.pop())

        // Get random idx from remaining unshuffled names
        const idxNameToRemove = Math.floor(Math.random() * this.namesListCleaned.length)

        // Remove randomly chosen name and push to shuffled list
        this.namesListProcessed.push(...this.namesListCleaned.splice(idxNameToRemove, 1))
      }
    },
  }
}
</script>

<template>
<h1>Random partner generator</h1>

<el-card class="box-card">
    <!-- Form -->
    <template v-if="isFormVisible">
      <el-form :model="form">
        <el-form-item label="Participant names">
          <el-input
            v-model="form.names"
            type="textarea"
            placeholder="Enter comma or newline-separated list of names to pair"
          />
        </el-form-item>
        <el-form-item>
          <el-button type="success" round @click="generateRotation">
            Round robin
          </el-button>
          <el-button type ="primary" round @click="generatePairs">Pairs</el-button>
        </el-form-item>
      </el-form>
    </template>

    <!-- Table -->
    <template v-else>
        <el-table :data="tableData" stripe style="width: 100%">
        <el-table-column prop="name1" :label="tableColumnALabel" />
        <el-table-column prop="name2" :label="tableColumnBLabel" />
      </el-table>

      <div class="bottom">
        <el-button
          type="warning"
          plain
          round
          @click="clearData"
        >
          Start over
        </el-button>
      </div>
    </template>

</el-card>
</template>

<style>
.bottom {
  margin-top: 13px;
  line-height: 12px;
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
</style>
