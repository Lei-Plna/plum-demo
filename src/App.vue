<script setup lang="ts">
interface Point {
  x: number
  y: number
}
interface Branch {
  start: Point
  length: number
  theta: number
}

const WIDTH = 600
const HEIGHT = 600
const canvas = $ref<HTMLCanvasElement>()!
const framesTask: (() => void)[] = []
function setup() {
  const startBranch = {
    start: {
      x: WIDTH / 2,
      y: HEIGHT,
    },
    length: 20,
    theta: Math.PI / 2,
  }
  step(startBranch)
  startFrame()
}

function step(branch: Branch, depth = 0): void {
  const previousEnd = getEndPoint(branch)
  drawBranch(branch)
  if (depth < 3 || Math.random() < 0.5) {
    framesTask.push(() => step({
      start: previousEnd,
      length: branch.length + Math.random() * 10 - 5,
      theta: branch.theta + Math.random() * 0.3,
    }, depth + 1))
  }

  if (depth < 3 || Math.random() < 0.5) {
    framesTask.push(() => step({
      start: previousEnd,
      length: branch.length + Math.random() * 10 - 5,
      theta: branch.theta - Math.random() * 0.3,
    }, depth + 1))
  }
}

function frame() {
  const tasks = [...framesTask]
  framesTask.length = 0
  tasks.forEach(task => task())
}

let frameCount = 0
function startFrame() {
  frameCount++
  frameCount % 6 === 0 && frame()
  requestAnimationFrame(() => {
    startFrame()
  })
}

function getEndPoint(branch: Branch): Point {
  return {
    x: branch.start.x + branch.length * Math.cos(branch.theta),
    y: branch.start.y - branch.length * Math.sin(branch.theta),
  }
}

function drawBranch(branch: Branch): void {
  const end = {
    x: branch.start.x + branch.length * Math.cos(branch.theta),
    y: branch.start.y - branch.length * Math.sin(branch.theta),
  }
  lineTo(branch.start, end)
}

function lineTo(start: Point, end: Point): void {
  const ctx = canvas.getContext('2d')!
  ctx.moveTo(start.x, start.y)
  ctx.lineTo(end.x, end.y)
  ctx.stroke()
}

onMounted(() => {
  setup()
})
</script>

<template>
  <div h-full w-full flex flex-justify-center flex-items-center>
    <canvas ref="canvas" width="600" height="600" border />
  </div>
</template>

<style lang="css" scoped></style>
