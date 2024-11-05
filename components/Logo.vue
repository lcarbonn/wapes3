<template>
  <div class="WapesLogo">
    <div class="Bubble Bubble-one" />
    <div class="SmallBubble Bubble-two" />
  </div>
</template>

<style>
.WapesLogo {
  display: inline-block;
  position: relative;
  overflow: hidden;
  height: 150px;
  width: 150px;
  border-radius: 75px;
}

.Bubble {
  position: absolute;
  top: 0;
  left: 0;
  width: 0;
  height: 0;
}

.SmallBubble {
  position: absolute;
  top: 150px;
  left: 50px;
  width: 0;
  height: 0;
}

.Bubble-one {
  height: 150px;
  width: 150px;
  animation: turn 2s linear forwards 1s;
  transform: rotateX(180deg);
  border-radius: 75px;
  background-color: #3986c4;
  background-image: radial-gradient(circle closest-side, white 10%,#3986c4 40%, #3986c4 50%);
}

.Bubble-two {
  height: 70px;
  width: 70px;
  animation: godown 2s ease-in-out forwards 1s;
  border-radius: 35px;
  background-color: #3986c4;
  background-image: radial-gradient(#3986c4 10%, white 90%);
}


@keyframes turn {
  100% {
    transform: rotateX(0deg);
  }
}

@keyframes godown {
  100% {
    top: 80px;
    left: 80px;
  }
}

@keyframes godup {
  100% {
    top: 0px;
  }
}

@keyframes goright {
  100% {
    left: 70px;
  }
}
</style>
