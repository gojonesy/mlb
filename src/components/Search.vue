<template>
<v-card>
   <v-card>
      <v-card-title class="headline">Player Search
      </v-card-title>
      <v-card-text>
         search here
      </v-card-text>
      <v-divider></v-divider>
      <v-expand-transition>
         <v-list v-if="model">
            <v-list-tile v-for="(field, i) in fields" :key="i">
               <v-list-tile-content>
                  <v-list-tile-title v-text="field.value"></v-list-tile-title>
                  <v-list-tile-sub-title v-text="field.key"></v-list-tile-sub-title>
               </v-list-tile-content>
            </v-list-tile>
         </v-list>
      </v-expand-transition>
      <v-card-actions>
         <v-spacer></v-spacer>
         <v-btn :disabled="!model" 
                 color="grey darken-3" 
                 @click="model = null">
                 Clear
                 <v-icon right>close</v-icon>
         </v-btn>
      </v-card-actions>
   </v-card>

   <p></p>
</v-card>
   
   
</template>

<script>
export default {
   data: () => ({
      descriptionLimit: 60,
      entries: [],
      isLoading: false,
      model: null,
      search: null
   }),
   computed: {
      fields () {
         if (!this.model) return []

         return Object.keys(this.model).map(key => {
            return {
               key, 
               value: this.model[key] || 'n/a'
            }
         })
      },
      items () {
         return this.entries.map(entry => {
            console.log(this.entry)
            const Description = entry.name_display_last_first.length > this.descriptionLimit ? entry.name_display_last_first.slice(0, this.descriptionLimit) + '...' 
            : entry.name_display_last_first

            return Object.assign({}, entry, { Description })
         })
      }
   },
   watch: {
      search (val) {
         // Items have already been loaded
         if (this.items.length > 0) return

         // Items have already been requested
         if (this.isLoading) return

         this.isLoading = true

         // lazily load input items
         fetch('http://lookup-service-prod.mlb.com/json/named.search_player_all.bam?sport_code=%27mlb%27&active_sw=%27Y%27&name_part=%27' + this.search + '%25%27')
            .then(res => res.json())
            .then(res => {
               
               this.count = res.search_player_all.queryResults.totalSize
               this.entries = res.search_player_all.queryResults.row
               console.log(this.entries[0].name_display_last_first)
            })
            .catch(err => {
               console.log(err)
            })
            .finally(() => (this.isLoading = false))
      }
   }
}
</script>

<style>

</style>
