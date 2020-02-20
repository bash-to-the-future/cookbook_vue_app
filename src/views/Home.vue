<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h1>New Recipe Form</h1>
    <div>
      Title: <input type="text" v-model="newRecipeTitle">
      PrepTime: <input type="text" v-model="newRecipePrepTime">
      Ingredients: <input type="text" v-model="newRecipeIngredients">
      Directions: <input type="text" v-model="newRecipeDirections">
      ImageUrl: <input type="text" v-model="newRecipeImageUrl">
      <button v-on:click="createRecipe()">Submit Recipe</button>
    </div>
    <div v-for="recipe in recipes">
      <h2>{{ recipe.title }}</h2>
      <p>Ingredients: {{ recipe.ingredients }}</p>
      <p>Directions: {{ recipe.directions }}</p>
      <img v-bind:src="recipe.image_url" v-bind:alt="recipe.title">
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
    }
  }
};
</script>