<template>
  <div v-if="modelValue" class="box arrow-top">
    {{text}}
  </div>
</template>

<script>
export default {
    name: "PopupBox",
    props: ["modelValue", "text"],
}
</script>

<style>
.box {
  width: 180px;
  height: auto;
  background-color: black;
  color: #fff;
  padding: 20px;
  position: relative;
  margin: 40px;
}

.box.arrow-top {
  margin-top: 40px;
  position: absolute;
  transform: translate(-100%, 0%);
}

.box.arrow-top:after {
  content: " ";
  position: absolute;
  right: 30px;
  top: -15px;
  border-top: none;
  border-right: 15px solid transparent;
  border-left: 15px solid transparent;
  border-bottom: 15px solid black;
}

@media only screen and (min-width:240px) and (max-width: 768px){
    .box{
        display: block;
        float: none;
        margin: 40px auto;
    }
}

</style>