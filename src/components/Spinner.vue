<template>
  <div class="loader"></div>
</template>

<script>
export default {
  name: 'Spinner',
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.loader {
  font-size: 10px;
  margin: 50px auto;
  text-indent: -9999em;
  width: 11em;
  height: 11em;
  border-radius: 50%;
  background: #fff;
  background: linear-gradient(to right, #fff 10%, rgba(255, 255, 255, 0) 42%);
  position: relative;
  animation: load 1s infinite linear;
  transform: translateZ(0);
}
.loader:before {
  width: 50%;
  height: 50%;
  background: #4299e1;
  border-radius: 100% 0 0 0;
  position: absolute;
  top: 0;
  left: 0;
  content: '';
}
.loader:after {
  background: #fff;
  width: 75%;
  height: 75%;
  border-radius: 50%;
  content: '';
  margin: auto;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}
@keyframes load {
  0%   { transform: rotate(0deg);   }
  100% { transform: rotate(360deg); }
}
</style>
