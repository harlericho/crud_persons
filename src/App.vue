<template>
  <Persons />
</template>

<script>
import Persons from "./components/Persons.vue";
export default {
  name: "App",
  components: {
    Persons,
  },
};
</script>

<style>
h3{
    font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
    color: darkolivegreen;
}
#btnEdit{
 margin: 5px;
}
#tablePersons{
  text-align: center;
  color: blue;
}
#dataEmail{
  color: dodgerblue;
}
#dataDni{
  color: darkgrey;
  font-style: oblique;
}
#img{
  border: 1px solid;
}
#viewInputImage{
  border: 1px solid;
}
</style>
