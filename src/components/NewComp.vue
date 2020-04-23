<template>
  <div>
    <div class="input-container">
      <input class="text-input" type="text" placeholder="Name" v-model="inputText" />
      <input class="text-input" type="text" placeholder="Age" v-model="inputAge" />
      <input class="text-input" type="text" placeholder="Color" v-model="inputColor" />
      <select class="text-input" placeholder="Gender" name v-model="inputGender">
        <option value>Gender</option>
        <option value="Male">Male</option>
        <option value="Female">Female</option>
      </select>
      <button class="add-text" @click="addItem">+add</button>
    </div>
    <div v-show="message" class="message-box">{{message}}</div>
    <div
      v-if="selectedPerson"
    >{{selectedPerson.name}} is {{selectedPerson.age}} years old. {{getGender(selectedPerson.gender)}} Favorite color is {{selectedPerson.color}}.</div>
    <div v-if="peopleLoaded">
      <div
        v-if="selectedPerson === undefined"
        class="click-prompt"
      >Click on a person to get details.</div>
    </div>
    <div class="edit-fields" v-if="personToEdit">
      <div class="edit-field">
        <input type="text"  v-model="personToEdit.name"/>
        <input type="text"  v-model="personToEdit.age" />
        <input type="text"  v-model="personToEdit.color" />
        <input type="text" v-model="personToEdit.gender" />
      </div>
      <div class="edit-options">
        <button class="cancel" @click="personToEdit = undefined">Cancel</button>
        <button class="save" @click="saveEdits">Save</button>
      </div>
    </div>

    <ul>
      <div class="item-wrapper" v-for="person in people" :key="person.id">
        <div
          class="list-item person-name"
          :class="{active: selectedPerson === person}"
          @click="selectedPerson = person"
        >{{ person.name | capitalize }}</div>
        <div class="edit" @click="personToEdit = person">Edit</div>
        <div class="delete" @click="deletePerson(person)">Delete</div>
      </div>
    </ul>
  </div>
</template>

<script>
const uuidv1 = require("uuid/v1");
const ourPeople = [
  {
    id: uuidv1(),
    name: "Terence",
    age: "25",
    color: "Blue",
    gender: "male"
  },
  {
    id: uuidv1(),
    name: "Paul",
    age: "21",
    color: "Black",
    gender: "male"
  },
  {
    id: uuidv1(),
    name: "Edward",
    age: "30",
    color: "brown   ",
    gender: "male"
  },
  {
    id: uuidv1(),
    name: "Kelly",
    age: "19",
    color: "yellow",
    gender: "female"
  }
];

export default {
  name: "NewComp",
  props: {
    firstProp: String
  },
  data() {
    return {
      people: [],
      peopleLoaded: false,
      selectedPerson: undefined,
      personToEdit: undefined,
      message: "",
      inputText: "",
      inputGender: "",
      inputAge: "",
      inputColor: ""
    };
  },
  methods: {
    getGender(value) {
      if (value.toLowerCase() === "male") {
        return "His";
      } else if (value.toLowerCase() === "female") {
        return "Her";
      }
    },
    async getPeople() {
      return new Promise(resolve => {
        setTimeout(() => resolve(ourPeople), 1500);
      });
    },
    async loadPeople() {
      this.people = [];
      this.message = "Getting People, Please be Patient...";
      this.people = await this.getPeople();
      this.peopleLoaded = true;
      this.message = "";
    },
    addItem() {
      if (
        this.inputText === "" ||
        this.inputGender === "" ||
        this.inputAge === "" ||
        this.inputColor === ""
      ) {
        return null;
      } else {
        this.people.push({
          id: uuidv1(),
          name: this.inputText,
          age: this.inputAge,
          color: this.inputColor,
          gender: this.inputGender
        });

        this.inputText = "";
        this.inputGender = "";
        this.inputAge = "";
        this.inputColor = "";
      }
    },
    deletePerson(person) {
      const personIndex = this.people.indexOf(person);
      this.people.splice(personIndex, 1);
    },
    saveEdits(){
        const getIndex =  this.people.findIndex(p => p.id === this.personToEdit.id)
        this.people.splice(getIndex, 1, this.personToEdit)
        this.personToEdit = undefined
    }
  },
  filters: {
    capitalize: function(value) {
      return (
        value
          .toString()
          .charAt(0)
          .toUpperCase() + value.slice(1)
      );
    }
  },
  created() {
    this.loadPeople();
  }
};
</script>

<style lang="scss" scoped>
ul {
  padding: 0;
}
.click-prompt {
  color: red;
}
.item-wrapper {
  margin-bottom: 5px;
  display: flex;
  justify-content: center;
}
.list-item {
  padding: 1rem;
  border: 1px solid #ccc;
  list-style: none;
  list-style-position: inside;
  cursor: pointer;
  flex: 4;
}
.list-item.active {
  background-color: #5f9ea0;
}
.edit {
  background: gray;
  color: #fff;
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  &:hover {
    background-color: #a4a1a1;
  }
}
.delete {
  background: orangered;
  color: #fff;
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  &:hover {
    background-color: #f36936;
  }
}
.message-box {
  padding: 2rem;
  background: teal;
  color: white;
}
.input-container {
  width: 100%;
  overflow: auto;
  margin: auto;
  display: flex;
  justify-content: center;
  margin-bottom: 1rem;
  flex-wrap: wrap;
  .text-input {
    padding: 1rem;
  }
  .add-text {
    padding: 1rem;
    background: green;
    color: #fff;
  }
}

.edit-fields {
  .edit-field {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    input {
      flex-basis: 40%;
      padding: 1rem;
    }
  }
  .edit-options {
    button {
      padding: 1rem;
      color: #fff;
    }
    .cancel {
      background: orangered;
      &:hover {
        background-color: #f36936;
      }
    }
    .save {
      background: green;
      &:hover {
        background: #2c702c;
      }
    }
  }
}
</style>