<template>
  <div>
    <b-container>
      <b-row>
        <!-- v-for dirketiva sluzi da renderuje nesto sto je itreabilno bez kopiranja koda, npr. -->
        <!-- Pokazati ovaj primer kako radi: <div v-for="item in 5" :key="item.id">{{ item }}</div> -->
        <!-- :key sluzi da jedinstveno indentifikuje item u listi -->
        <b-col xs="12" sm="6" md="4" v-for="list in lists" :key="list.id">
          <div class="controls">
            <!-- Kada se klikne na ovaj badge koji sluzi za brisanje, poziva  se metoda delteList, .prevent sluzi da
            ne bi se izvrsio href-->
            <b-badge @click.prevent="deleteList(list)" href="#" variant="danger" pill>X</b-badge>
          </div>
          <b-card-group deck>
            <b-card class="mt-5 mx-1" bg-variant="light" no-body :header="list.title">
              <b-list-group class="mt-3" flush>
                <!-- Ovde prosledjujemo listu itema ListItem komponenti koja u sebi
                renderuje stavke u odnosu na listu i osluskujemo na event
                update-lists koji ako se desi pozivamo updateUi metodu-->
                <ListItem @update-lists="updateUi" :list="list" />
              </b-list-group>
            </b-card>
          </b-card-group>
        </b-col>
        <b-col xs="12" sm="6" md="4">
          <b-card-group deck>
            <b-card class="mt-5 mx-1" no-body>
              <b-input-group prepend="New list">
<b-form-input v-model="newListTitle"></b-form-input>
                <b-input-group-append>
                  <b-button @click="addList" size="sm" text="Add" variant="success">Add</b-button>
                </b-input-group-append>
              </b-input-group>
            </b-card>
          </b-card-group>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import ListItem from "@/components/ListItem.vue";
export default {
  components: {
    ListItem
  },

  methods: {
    // this.$store.state.baseUrl uzimanje URL-a do backa
    async updateUi() {
      const response = await this.$http.get(
        `${this.$store.state.baseUrl}/liste.json`
      );
      this.lists = response.data;
    },
    async deleteList(list) {
      await this.$http.delete(`${this.$store.state.baseUrl}/liste/${list.id}`);
      this.updateUi();
    },

    async addList() {
      if (this.newListTitle.length < 1) return;
      await this.$http.post(`${this.$store.state.baseUrl}/liste`, {
        title: this.newListTitle
      });
      this.newListTitle = "";
      this.updateUi();
    }
  },
  async mounted() {
    this.updateUi();
  },
  data() {
    return {
      lists: [],
      newListTitle: ""
    };
  }
};
</script>

<style scoped>
.controls {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  position: absolute;
  width: 100%;
  top: 40px;
  left: 0;
  z-index: 1;
}
@media (max-width: 576px) {
  .controls {
    padding: 0 10px;
  }
}
</style>
