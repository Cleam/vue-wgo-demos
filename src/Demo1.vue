<template>
  <!-- Dropdown menu for selecting tool -->
  <select ref="tool" style="display: block; margin-bottom: 10px">
    <option value="black" selected>黑子</option>
    <option value="white">白子</option>
    <option value="SQ">正方形</option>
    <option value="TR">三角形</option>
    <option value="CR">圆形</option>
    <option value="plane">飞机</option>
    <option value="remove">删除</option>
  </select>
  <div ref="board">
    <!-- board will go here -->
  </div>
</template>

<script>
import WGo from './utils/wgo';
import bgImg from './assets/wood_1024.jpg';
export default {
  mounted() {
    console.log(WGo.DIR);
    const board = new WGo.Board(this.$refs.board, {
      // size: 19,
      width: 600,
      background: bgImg,
      section: {
        top: -0.5,
        left: -0.5,
        // top: 12,
        // left: 6,
        right: -0.5,
        bottom: -0.5,
      },
    });
    const plane = {
      // draw on stone layer
      stone: {
        // draw function is called in context of CanvasRenderingContext2D, so we can paint immediately using this
        draw: function (args, board) {
          const xr = board.getX(args.x), // get absolute x coordinate of intersection
            yr = board.getY(args.y), // get absolute y coordinate of intersection
            sr = board.stoneRadius; // get field radius in px
          console.log(xr, yr, sr);
          // if there is a black stone, draw white plane
          if (board.obj_arr[args.x][args.y][0].c == WGo.B) {
            this.strokeStyle = 'white';
          } else {
            this.strokeStyle = 'black';
          }

          this.lineWidth = 3;

          this.beginPath();

          this.moveTo(xr - sr * 0.8, yr);
          this.lineTo(xr + sr * 0.5, yr);
          this.lineTo(xr + sr * 0.8, yr - sr * 0.25);
          this.moveTo(xr - sr * 0.4, yr);
          this.lineTo(xr + sr * 0.3, yr - sr * 0.6);
          this.moveTo(xr - sr * 0.4, yr);
          this.lineTo(xr + sr * 0.3, yr + sr * 0.6);

          this.stroke();
        },
      },
    };
    const tool = this.$refs.tool; // get the <select> element
    board.addEventListener('click', function (x, y) {
      if (tool.value == 'black') {
        board.addObject({
          x: x,
          y: y,
          c: WGo.B,
        });
      } else if (tool.value == 'white') {
        board.addObject({
          x: x,
          y: y,
          c: WGo.W,
        });
      } else if (tool.value == 'remove') {
        board.removeObjectsAt(x, y);
      } else if (tool.value == 'plane') {
        board.addObject({
          x: x,
          y: y,
          type: plane,
        });
      } else {
        board.addObject({
          x: x,
          y: y,
          type: tool.value,
        });
      }
    });

    var coordinates = {
      // draw on grid layer
      grid: {
        draw: function (args, board) {
          var ch, t, xright, xleft, ytop, ybottom;

          this.fillStyle = 'rgba(0,0,0,0.7)';
          this.textBaseline = 'middle';
          this.textAlign = 'center';
          this.font = board.stoneRadius + 'px ' + (board.font || '');

          xright = board.getX(-0.75);
          xleft = board.getX(board.size - 0.25);
          ytop = board.getY(-0.75);
          ybottom = board.getY(board.size - 0.25);

          for (var i = 0; i < board.size; i++) {
            ch = i + 'A'.charCodeAt(0);
            if (ch >= 'I'.charCodeAt(0)) ch++;

            t = board.getY(i);
            this.fillText(board.size - i, xright, t);
            this.fillText(board.size - i, xleft, t);

            t = board.getX(i);
            this.fillText(String.fromCharCode(ch), t, ytop);
            this.fillText(String.fromCharCode(ch), t, ybottom);
          }

          this.fillStyle = 'black';
        },
      },
    };
    board.addCustomObject(coordinates);
  },
  setup() {},
};
</script>

<style></style>
