<template>
  <v-container fluid fill-height>
    <v-row>
      <v-col>
        <h1>Resident Demands</h1>
      </v-col>
    </v-row>

    <!-- Resident Demands -->
    <v-row>
      <v-col>
        <v-card>
          <v-tabs
            v-model="currentTab"
            centered
            background-color="secondary"
            active-class="primary"
            dark
            icons-and-text
            grow
          >
            <v-tabs-slider color="secondary"></v-tabs-slider>

            <v-tab key="population">
              Population
              <v-avatar color="grey lighten-2">
                <img src="@/assets/population/engineers.webp" alt="avatar" />
              </v-avatar>
            </v-tab>
            <v-tab key="residence">
              Residence
              <v-avatar>
                <img
                  src="@/assets/buildings/farmers/residence.webp"
                  alt="avatar"
                />
              </v-avatar>
            </v-tab>
          </v-tabs>
          <v-tabs-items v-model="currentTab" class="tabs-items-border">
            <v-tab-item key="population">
              <v-card flat>
                <v-card-text>
                  <population-input></population-input>
                </v-card-text>
              </v-card>
            </v-tab-item>
            <v-tab-item key="residence">
              <v-card flat>
                <v-card-text>
                  <residence-input></residence-input>
                </v-card-text>
              </v-card>
            </v-tab-item>
          </v-tabs-items>
        </v-card>
      </v-col>
    </v-row>
    <!-- Demands as table -->
    <resident-demands-table></resident-demands-table>
  </v-container>
</template>

<script>
import PopulationInput from '@/components/resident_demands/PopulationInput'
import ResidenceInput from '@/components/resident_demands/ResidenceInput'
import ResidentDemandsTable from '@/components/resident_demands/ResidentDemandsTable'
import residentDemandCalculatorMixin from '@/components/resident_demands/residentDemandCalculatorMixin.js'

export default {
  name: 'ResidentDemands',
  components: {
    'resident-demands-table': ResidentDemandsTable,

    'population-input': PopulationInput,
    'residence-input': ResidenceInput
  },

  beforeRouteEnter (to, from, next) {
    // do sth
    next(vm => {
      if (vm.$route.query.linkedFromChains === true) {
        const newPop = vm.$route.query.populationToAdd
        vm.$store.commit('addToPopulationDemands', newPop)
      }
    })
  },

  mixins: [residentDemandCalculatorMixin],

  computed: {
    /**
     * Convert the tab IDs (0 or 1) to tab "names" (0 = "population"; 1 = "residence")
     */
    currentTab: {
      get: function () {
        // Convert tab names (string) from storage to numeric tab ids
        switch (this.$store.state.config.demands.population_input_tab) {
          case 'population':
            return 0
          case 'residence':
            return 1
          default:
            console.error(
              'Unknown tab from store ' +
                this.$store.state.config.demands.population_input_tab
            )
            return -1
        }
      },
      set: function (newTab) {
        // Convert numeric tab ids for storage to tab names as string
        switch (newTab) {
          case 0:
            this.$store.commit('set_population_tab', 'population')
            break
          case 1:
            this.$store.commit('set_population_tab', 'residence')
            break
          default:
            console.error('Unknown tab ' + newTab)
        }
      }
    }
  },

  data: function () {
    return {}
  }
}
</script>

<style lang="scss" scoped>
.tabs-items-border {
  border: 3px solid var(--v-primary-base); /* See: https://vuetifyjs.com/en/features/theme/#custom-properties */
  border-top: none;
}
</style>
