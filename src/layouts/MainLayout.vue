<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn flat dense round icon="menu" aria-label="Menu" @click="leftDrawerOpen = !leftDrawerOpen"/>
        <q-toolbar-title>
          Projeto - Roleta Promocional
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered content-class="bg-grey-1">
      <q-list>
        <q-item-label header class="text-grey-8">
          Links Essenciais
        </q-item-label>

        <q-item to="/roulette" exact clickable v-ripple>
          <q-item-section avatar>
            <q-icon name="play_circle_filled" />
          </q-item-section>
          <q-item-section>
            Roleta
          </q-item-section>
        </q-item>

        <EssentialLink v-for="link in essentialLinks" :key="link.title" v-bind="link"/>

      </q-list>
      <q-btn class="button-floating" id="gift" title="PROMOÇÂO!" v-on:click="openModel('left')">
        <div class="button-floating-gift">
          <q-icon name="loyalty" />
        </div>
      </q-btn>
    </q-drawer>

    <q-dialog v-model="dialog" :position="position" v-on:mouseenter="rotate()" >
      <q-card class="text-center">
        <q-card-section>
          <div class="container" >
              <div class="col">
                <div class="text-primary q-mt-sm" style="font-size: 35px">
                  Roleta de Prêmios! <br/>
                  <div class="flex content-stretch">
                    <canvas id="canvas" width="500" height="500"></canvas>
                  </div>
                  <input type="button" class="roulette button q-mt-md" value="Rodar Roleta" id='spin' v-on:click="spin"/>
<!--                  <div>-->
<!--                    <q-input v-model="new_option" v-on:keyup.enter="addOptions" class="col q-mt-xl" filled square bg-color="white" label="Novo item" dense>-->
<!--                      <template v-slot:append>-->
<!--                        <q-btn round dense flat icon="add" />-->
<!--                      </template>-->
<!--                    </q-input>-->
<!--                  </div>-->
<!--                  <q-btn outline rounded class="button text-primary q-mb-sm" v-on:click="addOptions">Adicionar item</q-btn> <br/>-->
<!--                  <q-btn outline rounded class="button text-red" v-on:click="removeOptions(option)">Remover último item</q-btn>-->

                </div>
              </div>
          </div>
        </q-card-section>
      </q-card>
    </q-dialog>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink'
export default {
  name: 'MainLayout',
  components: {
    EssentialLink
  },
  data () {
    return {
      options: ['R$100,00', 'R$50,00', 'R$10,00', 'R$20,00', 'R$30,00'],
      new_option: '',
      option: '',
      startAngle: 0,
      startAngleStart: 0,
      spinTimeout: null,
      spinArcStart: 10,
      spinTime: 0,
      spinTimeTotal: 0,
      ctx: '',
      position: 'left',
      dialog: false,
      leftDrawerOpen: false,
      essentialLinks: [
        {
          title: 'Github',
          caption: 'github.com/lucasandrade95',
          icon: 'code',
          link: 'https://github.com/lucasandrade95'
        }
      ]
    }
  },
  computed: {
    arc: function () {
      return Math.PI / (this.options.length / 2)
    }
  },
  methods: {
    byte2Hex: function (n) {
      var nybHexString = '0123456789ABCDEF'
      return String(nybHexString.substr((n >> 4) & 0x0F, 1)) + nybHexString.substr(n & 0x0F, 1)
    },
    RGB2Color: function (r, g, b) {
      return '#' + this.byte2Hex(r) + this.byte2Hex(g) + this.byte2Hex(b)
    },
    getColor: function (item, maxitem) {
      var phase = 0
      var center = 128
      var width = 127
      var frequency = Math.PI * 2 / maxitem

      const red = Math.sin(frequency * item + 2 + phase) * width + center
      const green = Math.sin(frequency * item + phase) * width + center
      const blue = Math.sin(frequency * item + 4 + phase) * width + center

      return this.RGB2Color(red, green, blue)
    },
    addOptions: function () {
      if (this.new_option === '') {
        alert('Adicione algum valor!')
      } else {
        this.options.push(this.new_option)
        this.new_option = ''
        this.drawRouletteWheel()
      }
    },
    removeOptions: function (option) {
      const idx = this.options.indexOf(option) || 0
      this.options.splice(idx, 1)
      this.drawRouletteWheel()
    },
    drawRouletteWheel: function () {
      const canvas = document.getElementById('canvas')
      if (canvas.getContext) {
        var outsideRadius = 200
        var textRadius = 160
        var insideRadius = 125

        this.ctx = canvas.getContext('2d')
        this.ctx.clearRect(0, 0, 500, 500)

        this.ctx.lineWidth = 1

        this.ctx.font = 'bold 14px Helvetica, Arial'

        for (var i = 0; i < this.options.length; i++) {
          var angle = this.startAngle + i * this.arc
          this.ctx.fillStyle = this.getColor(i, this.options.length)

          this.ctx.beginPath()
          this.ctx.arc(250, 250, outsideRadius, angle, angle + this.arc, false)
          this.ctx.arc(250, 250, insideRadius, angle + this.arc, angle, true)
          this.ctx.stroke()
          this.ctx.fill()

          this.ctx.save()
          this.ctx.shadowOffsetX = -1
          this.ctx.shadowOffsetY = -1
          this.ctx.shadowBlur = 0
          this.ctx.fillStyle = 'black'
          this.ctx.translate(250 + Math.cos(angle + this.arc / 2) * textRadius,
            250 + Math.sin(angle + this.arc / 2) * textRadius)
          this.ctx.rotate(angle + this.arc / 2 + Math.PI / 2)
          var text = this.options[i]
          this.ctx.fillText(text, -this.ctx.measureText(text).width / 2, 0)
          this.ctx.restore()
        }

        this.ctx.fillStyle = 'black'
        this.ctx.beginPath()
        this.ctx.moveTo(250 - 4, 250 - (outsideRadius + 5))
        this.ctx.lineTo(250 + 4, 250 - (outsideRadius + 5))
        this.ctx.lineTo(250 + 4, 250 - (outsideRadius - 5))
        this.ctx.lineTo(250 + 9, 250 - (outsideRadius - 5))
        this.ctx.lineTo(250, 250 - (outsideRadius - 13))
        this.ctx.lineTo(250 - 9, 250 - (outsideRadius - 5))
        this.ctx.lineTo(250 - 4, 250 - (outsideRadius - 5))
        this.ctx.lineTo(250 - 4, 250 - (outsideRadius + 5))
        this.ctx.fill()
      }
    },
    rotate () {
      this.drawRouletteWheel()
    },
    spin: function () {
      this.spinAngleStart = Math.random() * 10 + 10
      this.spinTime = 0
      this.spinTimeTotal = Math.random() * 3 + 4 * 1000
      this.rotateWheel()
    },
    rotateWheel: function () {
      this.spinTime += 30
      if (this.spinTime >= this.spinTimeTotal) {
        this.stopRotateWheel()
        return
      }
      var spinAngle = this.spinAngleStart - this.easeOut(this.spinTime, 0, this.spinAngleStart, this.spinTimeTotal)
      this.startAngle += (spinAngle * Math.PI / 180)
      this.drawRouletteWheel()

      const _this = this
      this.spinTimeout = setTimeout(function () {
        _this.rotateWheel()
      }, 30)
    },
    stopRotateWheel: function () {
      clearTimeout(this.spinTimeout)
      var degrees = this.startAngle * 180 / Math.PI + 90
      var arcd = this.arc * 180 / Math.PI
      var index = Math.floor((360 - degrees % 360) / arcd)
      this.ctx.save()
      this.ctx.fillStyle = 'green'
      this.ctx.font = 'bold 18px Helvetica, Arial'
      var text = 'Você ganhou: ' + this.options[index] + '!'
      this.ctx.fillText(text, 250 - this.ctx.measureText(text).width / 2, 250 + 10)
      this.ctx.restore()
    },
    easeOut: function (t, b, c, d) {
      var ts = (t /= d) * t
      var tc = ts * t
      return b + c * (tc + -3 * ts + 3 * t)
    },
    openModel (position) {
      this.position = position
      this.dialog = true
    }
  }
}
</script>

<style>
  .button {
    display: inline-flex;
    flex-direction: column;
    align-items: stretch;
    position: relative;
    outline: 0;
    border: 0;
    vertical-align: middle;
    padding: 0;
    font-size: 14px;
    line-height: 1.715em;
    text-decoration: none;
    background: transparent;
    font-weight: 500;
    text-transform: uppercase;
    text-align: center;
    width: auto;
    height: auto;
  }
  .roulette {
    border-radius: 10px;
    color: white;
    background: #1976d2;
    padding: 10px;
    cursor: pointer
  }
  .button-floating {
    position: fixed;
    top: 419.5px;
    width: 50px;
    height: 100px;
    border: none;
    border-radius: 0px 90px 90px 0px;
    z-index: 1003;
    background-color: rgb(102, 23, 203);
    cursor: pointer;
    display: table;
  }
  .button-floating-gift {
    color: rgb(255, 255, 255);
  }
</style>
