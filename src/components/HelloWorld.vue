<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <v-form ref="form" v-model="valid" lazy-validation>
          <v-container>
            <h1 class="mb-3 text-left">Breeding Probability Calculator</h1>
            <v-row>
              <v-col class="pb-0" cols="6">
                <v-img :src="parentOne.srcImage" height="150" contain></v-img>
              </v-col>
              <v-col class="pb-0" cols="6">
                <v-img :src="parentTwo.srcImage" height="150" contain></v-img>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="6">
                <v-text-field
                  v-model="parentOne.id"
                  type="number"
                  :counter="8"
                  label="Parent 1"
                  prefix="#"
                  placeholder="Axie Id"
                  persistent-placeholder
                  autofocus
                  :rules="[(v) => !!v || 'Item is required.']"
                  required
                  @change="selectParentOne"
                ></v-text-field>
              </v-col>

              <v-col cols="6">
                <v-text-field
                  v-model="parentTwo.id"
                  type="number"
                  :counter="8"
                  max="8"
                  label="Parent 2"
                  prefix="#"
                  placeholder="Axie Id"
                  persistent-placeholder
                  :rules="[(v) => !!v || 'Item is required.']"
                  required
                  @change="selectParentTwo"
                ></v-text-field>
              </v-col>
            </v-row>
            <h4 class="text-left mb-3">Desired Axie</h4>
            <v-row class="pt-3">
              <v-col class="py-0" cols="12" md="4">
                <v-combobox
                  v-model="desiredParts.eyes"
                  :items="eyesList"
                  label="Eyes"
                  persistent-placeholder
                  dense
                  small-chips
                  outlined
                  multiple
                  :rules="[(v) => v.length > 0 || 'Select at least one.']"
                  required
                ></v-combobox>
              </v-col>
              <v-col class="py-0" cols="12" md="4">
                <v-combobox
                  v-model="desiredParts.ears"
                  :items="earsList"
                  label="Ears"
                  persistent-placeholder
                  dense
                  small-chips
                  outlined
                  multiple
                  :rules="[(v) => v.length > 0 || 'Select at least one.']"
                  required
                ></v-combobox>
              </v-col>
              <v-col class="py-0" cols="12" md="4">
                <v-combobox
                  v-model="desiredParts.mouth"
                  :items="mouthList"
                  label="Mouth"
                  persistent-placeholder
                  dense
                  small-chips
                  outlined
                  multiple
                  :rules="[(v) => v.length > 0 || 'Select at least one.']"
                  required
                ></v-combobox>
              </v-col>
              <v-col class="py-0" cols="12" md="4">
                <v-combobox
                  v-model="desiredParts.horn"
                  :items="hornList"
                  label="Horn"
                  persistent-placeholder
                  dense
                  small-chips
                  outlined
                  multiple
                  :rules="[(v) => v.length > 0 || 'Select at least one.']"
                  required
                ></v-combobox>
              </v-col>
              <v-col class="py-0" cols="12" md="4">
                <v-combobox
                  v-model="desiredParts.back"
                  :items="backList"
                  label="Back"
                  persistent-placeholder
                  dense
                  small-chips
                  outlined
                  multiple
                  :rules="[(v) => v.length > 0 || 'Select at least one.']"
                  required
                ></v-combobox>
              </v-col>
              <v-col class="py-0" cols="12" md="4">
                <v-combobox
                  v-model="desiredParts.tail"
                  :items="tailList"
                  label="Tail"
                  persistent-placeholder
                  dense
                  small-chips
                  outlined
                  multiple
                  :rules="[(v) => v.length > 0 || 'Select at least one.']"
                  required
                ></v-combobox>
              </v-col>
              <v-col class="py-0" cols="12">
                <v-combobox
                  v-model="desiredClass"
                  :items="classList"
                  label="Class"
                  persistent-placeholder
                  dense
                  small-chips
                  outlined
                  multiple
                  :rules="[(v) => v.length > 0 || 'Select at least one.']"
                  required
                ></v-combobox>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12 py-0">
                <v-btn
                  color="primary"
                  :loading="loading"
                  @click="$refs.form.validate() && fetchAxieGenes()"
                  >Compute Probability</v-btn
                >
              </v-col>
            </v-row>
          </v-container>
        </v-form>
      </v-col>
    </v-row>
    <v-divider class="my-4"></v-divider>
    <v-btn
      v-if="parentOne.genes != null && parentTwo.genes != null"
      small
      text
      @click="showPartProbabilities = !showPartProbabilities"
      ><v-icon left>mdi-toggle-switch</v-icon>Toggle Odds</v-btn
    >
    <v-expand-transition>
      <v-row v-if="showPartProbabilities && partProbabilities.tail.length > 0">
        <v-col
          class="pa-3 d-flex"
          cols="12"
          md="4"
          v-for="part in ['eyes', 'ears', 'mouth', 'horn', 'back', 'tail']"
          :key="part"
        >
          <v-card class="flex">
            <v-card-title class="py-0 text-capitalize">{{ part }}</v-card-title>
            <v-container fluid class="pa-0">
              <v-simple-table dense class="ma-0 pa-0">
                <template v-slot:default>
                  <tbody>
                    <tr
                      v-for="probability in partProbabilities[part]"
                      :key="probability[0]"
                    >
                      <td>{{ probability[0] }}</td>
                      <td>{{ probability[1] }}%</td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </v-container>
          </v-card>
        </v-col>
        <v-col class="pa-3 d-flex" cols="12">
          <v-card class="flex">
            <v-card-title class="py-0 text-capitalize">Class</v-card-title>
            <v-container fluid class="pa-0">
              <v-simple-table dense class="ma-0 pa-0">
                <template v-slot:default>
                  <tbody>
                    <tr
                      v-for="(item, index) in classProbabilities"
                      :key="index"
                    >
                      <td class="text-capitalize">{{ index }}</td>
                      <td>{{ item.toFixed(3) }}%</td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </v-container>
          </v-card>
        </v-col>
      </v-row>
    </v-expand-transition>

    <v-row v-if="this.probabilityPerBreed != null" class="mb-4">
      <v-col class="pa-3 d-flex" cols="12">
        <v-card class="flex">
          <v-card-title class="py-0 text-capitalize"
            >Breeding Probability</v-card-title
          >
          <v-container fluid class="pa-0">
            <v-simple-table dense class="ma-0 pa-0">
              <template v-slot:default>
                <thead>
                  <tr>
                    <th>Number of Breeds</th>
                    <th>Probability</th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="numberOfBreeds of [1, 2, 3, 4, 5, 6, 7]"
                    :key="numberOfBreeds"
                  >
                    <td>{{ numberOfBreeds }}</td>
                    <td>{{ getProbability(numberOfBreeds).toFixed(3) }}%</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-container>
        </v-card>
      </v-col>
    </v-row>

    <!-- <v-row v-if="parentOne.genes != null && parentTwo.genes != null">
      <v-col cols="12" md="6">
        <v-spacer></v-spacer>
        <v-card :loading="loading" class="mx-auto" max-width="374">
          <template slot="progress">
            <v-progress-linear
              color="deep-purple"
              height="10"
              indeterminate
            ></v-progress-linear>
          </template>
          <v-img height="150" :src="parentOne.srcImage" contain></v-img>
          <v-card-text class="d-flex py-0 text-capitalize">
            {{ parentOne.genes.cls }}
            <span class="ml-auto" outlined tile>Breed Count: {{ 6 }}</span>
          </v-card-text>
          <v-card-title class="py-0">Gene Details</v-card-title>
          <v-container fluid class="px-0 pt-0 pb-2">
            <v-simple-table dense class="ma-0 pa-0">
              <template v-slot:default>
                <thead>
                  <tr>
                    <th>D</th>
                    <th>R1</th>
                    <th>R2</th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="part in [
                      'eyes',
                      'ears',
                      'mouth',
                      'horn',
                      'back',
                      'tail',
                    ]"
                    :key="part"
                  >
                    <td>{{ parentOne.genes[part].d.name }}</td>
                    <td>{{ parentOne.genes[part].r1.name }}</td>
                    <td>{{ parentOne.genes[part].r2.name }}</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-container>
        </v-card>
      </v-col>
      <v-col cols="12" md="6">
        <v-spacer></v-spacer>
        <v-card :loading="loading" class="mx-auto" max-width="374">
          <template slot="progress">
            <v-progress-linear
              color="deep-purple"
              height="10"
              indeterminate
            ></v-progress-linear>
          </template>
          <v-img height="150" :src="parentTwo.srcImage" contain></v-img>
          <v-card-text class="d-flex py-0 text-capitalize">
            {{ parentTwo.genes.cls }}
            <span class="ml-auto" outlined tile>Breed Count: {{ 6 }}</span>
          </v-card-text>
          <v-card-title class="py-0">Gene Details</v-card-title>
          <v-container fluid class="px-0 pt-0 pb-2">
            <v-simple-table dense class="ma-0 pa-0">
              <template v-slot:default>
                <thead>
                  <tr>
                    <th>D</th>
                    <th>R1</th>
                    <th>R2</th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="i in [
                      'eyes',
                      'ears',
                      'mouth',
                      'horn',
                      'back',
                      'tail',
                    ]"
                    :key="i"
                  >
                    <td>{{ parentTwo.genes[i].d.name }}</td>
                    <td>{{ parentTwo.genes[i].r1.name }}</td>
                    <td>{{ parentTwo.genes[i].r2.name }}</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </v-container>
        </v-card>
      </v-col>
    </v-row> -->
  </v-container>
</template>

<script>
export default {
  name: "HelloWorld",
  data: () => ({
    valid: false,
    loading: false,
    parentOne: {
      id: "",
      srcImage:
        "https://static.vecteezy.com/system/resources/previews/004/141/669/original/no-photo-or-blank-image-icon-loading-images-or-missing-image-mark-image-not-available-or-image-coming-soon-sign-simple-nature-silhouette-in-frame-isolated-illustration-vector.jpg",
      genes: null,
    },
    parentTwo: {
      id: "",
      srcImage:
        "https://static.vecteezy.com/system/resources/previews/004/141/669/original/no-photo-or-blank-image-icon-loading-images-or-missing-image-mark-image-not-available-or-image-coming-soon-sign-simple-nature-silhouette-in-frame-isolated-illustration-vector.jpg",
      genes: null,
    },
    desiredParts: {
      eyes: [],
      ears: [],
      mouth: [],
      horn: [],
      back: [],
      tail: [],
    },
    desiredClass: [],
    classList: [
      "Aquatic",
      "Beast",
      "Bird",
      "Bug",
      "Dawn",
      "Dusk",
      "Mech",
      "Plant",
      "Reptile",
    ],
    cardsList: [],
    showPartProbabilities: false,
    partProbabilities: {
      eyes: {},
      ears: {},
      mouth: {},
      horn: {},
      back: {},
      tail: {},
    },
    classProbabilities: {},
    probabilityPerBreed: null,
  }),
  computed: {
    eyesList() {
      let invalidCards = ["Baby", "Risky Trunk", "Sparky"];
      return this.cardsList
        .filter(
          (card) => card.part == "eyes" && !invalidCards.includes(card.name)
        )
        .map((card) => card.name);
    },
    earsList() {
      let invalidCards = ["Little Crab", "Hidden Ears", "Foxy"];
      return this.cardsList
        .filter(
          (card) => card.part == "ears" && !invalidCards.includes(card.name)
        )
        .map((card) => card.name);
    },
    mouthList() {
      let invalidCards = ["Puff", "Beetroot", "Foxy", "Cub"];
      return this.cardsList
        .filter(
          (card) => card.part == "mouth" && !invalidCards.includes(card.name)
        )
        .map((card) => card.name);
    },
    hornList() {
      let invalidCards = ["Jellytacle", "Rusty Helm", "Persimmon", "Mandarine"];
      return this.cardsList
        .filter(
          (card) => card.part == "horn" && !invalidCards.includes(card.name)
        )
        .map((card) => card.name);
    },
    backList() {
      let invalidCards = [
        "Tiny Dino",
        "Succulent",
        "Forest Hero",
        "Magic Sack",
      ];
      return this.cardsList
        .filter(
          (card) => card.part == "back" && !invalidCards.includes(card.name)
        )
        .map((card) => card.name);
    },
    tailList() {
      let invalidCards = ["Puff", "Sprout", "Buba Brush", "Aegis Talisman"];
      return this.cardsList
        .filter(
          (card) => card.part == "tail" && !invalidCards.includes(card.name)
        )
        .map((card) => card.name);
    },
  },
  mounted() {
    this.fetchCards();
    this.parentOneImageLink = `https://axiecdn.axieinfinity.com/axies/11179588/axie/axie-full-transparent.png`;
  },
  methods: {
    fetchCards() {
      fetch(
        "https://7b00e2d6-641e-45cb-a705-390f8d062064.mock.pstmn.io/api/v1/cards"
      )
        .then((response) => response.json())
        .then((json) => (this.cardsList = json));
    },
    selectParentOne() {
      fetch(
        `https://axiecdn.axieinfinity.com/axies/${this.parentOne.id}/axie/axie-full-transparent.png`
      )
        .then((response) => {
          if (!response.ok) {
            throw Error(response.statusText);
          }
          return response;
        })
        .then((response) => {
          this.parentOne.srcImage = response.url;
        })
        .catch(() => {
          this.parentOne.srcImage =
            "https://static.vecteezy.com/system/resources/previews/004/141/669/original/no-photo-or-blank-image-icon-loading-images-or-missing-image-mark-image-not-available-or-image-coming-soon-sign-simple-nature-silhouette-in-frame-isolated-illustration-vector.jpg";
        });
    },
    selectParentTwo() {
      fetch(
        `https://axiecdn.axieinfinity.com/axies/${this.parentTwo.id}/axie/axie-full-transparent.png`
      )
        .then((response) => {
          if (!response.ok) {
            throw Error(response.statusText);
          }
          return response;
        })
        .then((response) => {
          this.parentTwo.srcImage = response.url;
        })
        .catch(() => {
          this.parentTwo.srcImage =
            "https://static.vecteezy.com/system/resources/previews/004/141/669/original/no-photo-or-blank-image-icon-loading-images-or-missing-image-mark-image-not-available-or-image-coming-soon-sign-simple-nature-silhouette-in-frame-isolated-illustration-vector.jpg";
        });
    },
    fetchAxieGenes() {
      this.loading = true;
      let doneParentOne = false;
      let doneParentTwo = false;
      fetch(`https://ronin.rest/ronin/axie/${this.parentOne.id}`)
        .then((response) => response.json())
        .then((json) => {
          this.parentOne.genes = json.genes;
          doneParentOne = true;
          if (doneParentTwo) {
            this.loading = false;
            this.fetchProbabilities();
          }
        })
        .catch(() => {
          this.parentOne.genes = null;
          this.loading = false;
        });

      fetch(`https://ronin.rest/ronin/axie/${this.parentTwo.id}`)
        .then((response) => response.json())
        .then((json) => {
          this.parentTwo.genes = json.genes;
          doneParentTwo = true;
          if (doneParentOne) {
            this.loading = false;
            this.fetchProbabilities();
          }
        })
        .catch(() => {
          this.parentTwo.genes = null;
          this.loading = false;
        });
    },
    fetchProbabilities() {
      this.partProbabilities = {
        eyes: {},
        ears: {},
        mouth: {},
        horn: {},
        back: {},
        tail: {},
      };
      let parts = ["eyes", "ears", "mouth", "horn", "back", "tail"];
      let probabilities = { d: 37.5, r1: 9.375, r2: 3.125 };

      parts.forEach((part) => {
        for (let geneType in probabilities) {
          if (
            this.parentOne.genes[part][geneType].name in
            this.partProbabilities[part]
          ) {
            this.partProbabilities[part][
              this.parentOne.genes[part][geneType].name
            ] += probabilities[geneType];
          } else {
            this.partProbabilities[part][
              this.parentOne.genes[part][geneType].name
            ] = probabilities[geneType];
          }

          if (
            this.parentTwo.genes[part][geneType].name in
            this.partProbabilities[part]
          ) {
            this.partProbabilities[part][
              this.parentTwo.genes[part][geneType].name
            ] += probabilities[geneType];
          } else {
            this.partProbabilities[part][
              this.parentTwo.genes[part][geneType].name
            ] = probabilities[geneType];
          }
        }

        let sortByProbability = [];
        for (let i in this.partProbabilities[part]) {
          sortByProbability.push([i, this.partProbabilities[part][i]]);
        }

        sortByProbability.sort(function (a, b) {
          return b[1] - a[1];
        });

        this.partProbabilities[part] = sortByProbability;
      });

      this.probabilityPerBreed = null;
      parts.forEach((part) => {
        let partProbability =
          this.partProbabilities[part].reduce((previousValue, currentValue) => {
            return this.desiredParts[part].includes(currentValue[0])
              ? previousValue + currentValue[1]
              : previousValue;
          }, 0) / 100;

        if (this.probabilityPerBreed == null) {
          this.probabilityPerBreed = partProbability;
        } else {
          this.probabilityPerBreed *= partProbability;
        }
      });

      let parentsArePure = true;
      for (let part of parts) {
        if (
          this.parentOne.genes[part].d.cls !== this.parentOne.genes.cls ||
          this.parentTwo.genes[part].d.cls !== this.parentTwo.genes.cls
        ) {
          parentsArePure = false;
          break;
        }
      }

      this.classProbabilities = {};
      let classProbability = 0;
      if (!parentsArePure) {
        if (this.parentOne.genes.cls !== this.parentTwo.genes.cls) {
          this.classProbabilities[this.parentOne.genes.cls] = 50;
          this.classProbabilities[this.parentTwo.genes.cls] = 50;
        } else {
          this.classProbabilities[this.parentOne.genes.cls] = 100;
        }

        if (
          this.desiredClass.includes(
            this.capitalize(this.parentOne.genes.cls)
          ) &&
          this.desiredClass.includes(this.capitalize(this.parentTwo.genes.cls))
        ) {
          classProbability = 1;
        } else if (
          this.desiredClass.includes(
            this.capitalize(this.parentOne.genes.cls)
          ) ||
          this.desiredClass.includes(this.capitalize(this.parentTwo.genes.cls))
        ) {
          classProbability = 0.5;
        }
      } else {
        let secretClassCombinations = {
          dusk: ["reptile", "aquatic"],
          mech: ["bug", "beast"],
          dawn: ["plant", "bird"],
        };

        for (let secretClass in secretClassCombinations) {
          if (this.parentOne.genes.cls === this.parentTwo.genes.cls) {
            this.classProbabilities[this.parentOne.genes.cls] = 100;
            break;
          }

          this.classProbabilities[this.parentOne.genes.cls] = (1 / 3) * 100;
          this.classProbabilities[this.parentTwo.genes.cls] = (1 / 3) * 100;
          if (
            secretClassCombinations[secretClass].includes(
              this.parentOne.genes.cls
            ) &&
            secretClassCombinations[secretClass].includes(
              this.parentTwo.genes.cls
            )
          ) {
            this.classProbabilities[secretClass] = (1 / 3) * 100;
            if (this.desiredClass.includes(this.capitalize(secretClass))) {
              classProbability = 1 / 3;
            }
            break;
          }
        }

        if (
          this.desiredClass.includes(this.capitalize(this.parentOne.genes.cls))
        ) {
          classProbability += 1 / 3;
        }
        if (
          this.desiredClass.includes(this.capitalize(this.parentTwo.genes.cls))
        ) {
          classProbability += 1 / 3;
        }
      }

      this.probabilityPerBreed *= classProbability;
    },
    getProbability(numberOfBreeds) {
      return (1 - (1 - this.probabilityPerBreed) ** numberOfBreeds) * 100;
    },
    capitalize(s) {
      return s[0].toUpperCase() + s.slice(1);
    },
  },
};
</script>
