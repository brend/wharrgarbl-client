<template>
  <Transition>
    <div 
      class="box"
      v-show="show"
    >
      
        <div>
          <h1>{{displayText}}</h1>
        </div>
    </div>
  </Transition>
</template>

<script>
export default {
    name: "MessageBox",
    props: ["text", "show"],
    data() {
      return {
        displayText: "",
      };
    },
    watch: {
      text(val) {
        if (!val) {
          setTimeout(() => {
            this.displayText = val;
          }, 500);
        } else {
          this.displayText = val;
        }
      },
    },
}
</script>

<style>
.box {
    position:absolute;
    top:50%;
    left:50%;
    transform:translate(-50%, -50%);
    color: white; background: #515151; opacity: .8;
    z-index: 1000;
    padding: 20px;
    border-radius: 18px;
}

.v-enter-active,
.v-leave-active {
    transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
    opacity: 0;
}
</style>