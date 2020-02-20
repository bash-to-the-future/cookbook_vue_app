<template>
  <div class="home">

    <h1>{{ message }}</h1>
    <h1>New Recipe Form</h1>
    <div class="new-form">
      Title: <input type="text" v-model="newRecipeTitle">
      PrepTime: <input type="text" v-model="newRecipePrepTime">
      Ingredients: <input type="text" v-model="newRecipeIngredients">
      Directions: <input type="text" v-model="newRecipeDirections">
      ImageUrl: <input type="text" v-model="newRecipeImageUrl">
      <button v-on:click="createRecipe()">Submit Recipe</button>
    </div> <!-- end of .new-form -->

    <h1>All Recipes</h1>

    <div v-for="recipe in recipes">
      <img v-on:click="showRecipe(recipe)" v-bind:src="recipe.image_url" v-bind:alt="recipe.title">
      <h2>{{ recipe.title }}</h2>
      <div class="show-page" v-if="recipe === currentRecipe">
        <h4>Prep Time: {{ recipe.formatted.prep_time }}</h4>
        <p>Ingredients: {{ recipe.ingredients }}</p>
        <p>Directions: {{ recipe.directions }}</p>

        <div class="edit-form">
          <h4>Edit Recipe</h4>

          <div>
            Title: <input type="text" v-model="recipe.title">
          </div>

          <div>
            Prep Time: <input type="text" v-model="recipe.prep_time">
          </div>

          <div>
            Ingredients: <input type="text" v-model="recipe.ingredients">
          </div>

          <div>
            Directions: <input type="text" v-model="recipe.directions">
          </div>

          <div>
            Image URL: <input type="text" v-model="recipe.image_url">
          </div>

          <button v-on:click="updateRecipe(recipe)">Update</button>
        </div> <!-- end of .edit-form -->

        <div class="destroy-action">
          <button v-on:click="destroyRecipe(recipe)">Destroy</button>
        </div>
      </div> <!-- end of .show-page --> 
    </div>

  </div>
</template>

<style>
</style>

<script>
// let axios = require("axios");
import axios from "axios";


export default {
  data: function() {
    return {
      currentRecipe: {},
      message: "Welcome to Vue.js!",
      recipes: [],
      newRecipeTitle: "",
      newRecipeChef: "",
      newRecipePrepTime: "",
      newRecipeIngredients: "",
      newRecipeDirections: "",
      newRecipeImageUrl: "",
    };
  },
  created: function() {
    axios.get("/api/recipes").then(response => {
      this.recipes = response.data;
    });
  },
  methods: {
    createRecipe: function() {
      console.log("Recipe created!");

      let params = {
        title: this.newRecipeTitle,
        chef: this.newRecipeChef,
        prep_time: this.newRecipePrepTime,
        ingredients: this.newRecipeIngredients,
        directions: this.newRecipeDirections,
        image_url: this.newRecipeImageUrl
      }

      axios
        .post("/api/recipes", params)
        .then(response => {
          // console.log(response.data);
          this.recipes.push(response.data);
        }).catch(error => {
          console.log(error.response);
        });
    },
    showRecipe: function(inputRecipe) {
      if (this.currentRecipe !== inputRecipe) {
        this.currentRecipe = inputRecipe;
      } else {
        this.currentRecipe = {};
      }

      // this.currentRecipe = this.currentRecipe !== inputRecipe ? inputRecipe : {}; // one line version
    },
    updateRecipe: function(inputRecipe) {
      var clientParams = {
        title: inputRecipe.title,
        prep_time: inputRecipe.prep_time,
        directions: inputRecipe.directions,
        ingredients: inputRecipe.ingredients,
        image_url: inputRecipe.image_url
      };

      axios
        .patch("/api/recipes/" + inputRecipe.id, clientParams)
        .then(response => {
          console.log("Success", response.data);
        }).catch(error => {
          console.log(error.response.data);
        });
    },
    destroyRecipe: function(inputRecipe) {
      axios 
        .delete("/api/recipes/" + inputRecipe.id)
        .then(response => {
          console.log("Success", response.data);
          var index = this.recipes.indexOf(inputRecipe);
          this.recipes.splice(index, 1);
        });
    }
  }
};
</script>