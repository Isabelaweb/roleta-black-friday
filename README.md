# roleta-black-friday

# PARA USAR A ROLETA BLACK FRIDAY

## Instalando a biblioteca nescessaria 
```sh
yarn add vue-winwheel
```

## Como ficou a roleta
após instalar o lib acima abra o seguinte arquivo `node_modules\vue-winwheel\vue-winwheel.vue` e troque pelo código abaixo

```vue
<template>
		<section class="vue-winwheel">
			<div class="mobile-container">
				<!-- <h1>ROLETA BLACK FRIDAY</h1> -->
				<div class="wheel-wrapper">
					<div class="canvas-wrapper">
						<canvas id="canvas" width="310" height="310">
							<p style="{color: white}" align="center">Sorry, your browser doesn't support canvas. Please try Google Chrome.</p>
						</canvas>
					</div>
					<div class="button-wrapper">
						<a class="btn btn-play" href="#" @click.prevent="startSpin()" v-if="!loadingPrize && !wheelSpinning">GIRAR!</a>
					</div>
				</div>
			</div>
			<div class="custom-modal modal-mask" id="modalSpinwheel" v-if="modalPrize">
				<div slot="body">
					<a href="" @click.prevent="hidePrize()" class="modal-dismiss">
						<i class="icon_close"></i>
					</a>
					<h2>
						Copie o cupom promocional e cole no campo CUPOM no carrinho!
					</h2>
					<input class="input-coupon" ref="receveidCoupon" :value="prizeName" :readonly="true"  />
          <button  class="copy-coupon-button" @click="copyCoupon()">copiar cupom</button>
				</div>
			</div>
		</section>
</template>


<script>
import * as Winwheel from './Winwheel'

export default {
  name: 'VueWinWheel',
  props:{
		segments:{
			default(){
				return [
					{
						textFillStyle: '#000',
						fillStyle: '#fadede',
						text:'Prize 8',
            coupon: 'brinde',
					}
				]
			}
		}
  },
  data () {
    return {
      loadingPrize: false,
      theWheel: null,
      modalPrize: false,
      wheelPower: 1,
      wheelSpinning: false,
      prizeName: 'BUY 1 GET 1',
      WinWheelOptions: {
        textFontSize: 14,
        outterRadius: 410,
        innerRadius: 25,
        lineWidth: 8,
        animation: {
          type: 'spinOngoing',
          duration: 0.5
        }
      }
    }
  },
  methods: {
    copyCoupon () {
      const currentCoupon = this.$refs.receveidCoupon;

      currentCoupon.select()

      document.execCommand("copy")
    },
    showPrize () {
      this.modalPrize = true
    },
    hidePrize () {
      this.modalPrize = false
    },
    startSpin () {
      if (this.wheelSpinning === false) {
        this.theWheel.startAnimation()
        this.wheelSpinning = true
        this.theWheel = new Winwheel.Winwheel({
          ...this.WinWheelOptions,
          numSegments: this.segments.length,
          segments: this.segments,
          animation: {
            type: 'spinToStop',
            duration: 5,
            spins: 5,
            callbackFinished: this.onFinishSpin
          }
        })

        // example input prize number get from Backend
        // Important thing is to set the stopAngle of the animation before stating the spin.

        var prizeNumber = Math.floor(Math.random() * this.segments.length) // or just get from Backend
        var stopAt = 360 / this.segments.length * prizeNumber - 360 / this.segments.length / 2 // center pin
        // var stopAt = 360 / this.segments.length * prizeNumber - Math.floor(Math.random() * 60) //random location
        this.theWheel.animation.stopAngle = stopAt
        this.theWheel.startAnimation()
        this.wheelSpinning = false
      }
    },
    resetWheel () {
      this.theWheel = new Winwheel.Winwheel({
        ...this.WinWheelOptions,
        numSegments: this.segments.length,
        segments: this.segments
      })

      if (this.wheelSpinning) {
        this.theWheel.stopAnimation(false) // Stop the animation, false as param so does not call callback function.
      }

      this.theWheel.rotationAngle = 0 // Re-set the wheel angle to 0 degrees.
      this.theWheel.draw() // Call draw to render changes to the wheel.
      this.wheelSpinning = false // Reset to false to power buttons and spin can be clicked again.
    },
    initSpin () {
      this.loadingPrize = true
            this.resetWheel()
            this.loadingPrize = false
    },
    onFinishSpin (indicatedSegment) {
      // this.prizeName = indicatedSegment.text
      this.prizeName = indicatedSegment.coupon
      this.showPrize()
    }
  },
  computed: {},
  updated () {},
  mounted () {
    this.initSpin()
    // this.resetWheel()
  },
  created () {}
}

</script>

<style scoped>
.vue-winwheel {
	text-align: center;
	background-image: url('/static/img/obstacle-run/bg-spinner-mobile.svg');
	background-size: cover;
	background-position: center bottom;
	background-repeat: no-repeat;
}
.vue-winwheel h1 {
	color: #b32656;
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	font-size: 36px;
	line-height: 90px;
	letter-spacing: 4px;
	margin: 0;
}
.vue-winwheel h2 {
	margin: 0;
}
.vue-winwheel #modalSpinwheel.custom-modal .content-wrapper .content {
	width: calc(100vw - 30px);
	padding-top: 52px;
}
.vue-winwheel #modalSpinwheel.custom-modal .content-wrapper .content h2 {
	text-transform: uppercase;
	color: #b32656;
	margin-bottom: 16px;
	margin-top: 0;
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	font-size: 18px;
	letter-spacing: 1.1px;
	margin: 0;
}
.vue-winwheel #modalSpinwheel.custom-modal .content-wrapper .content p {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	font-size: 14px;
	color: black;
	text-align: center;
	line-height: 25px;
}
.vue-winwheel #modalSpinwheel.custom-modal .content-wrapper .content p strong {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
.vue-winwheel #modalSpinwheel.custom-modal .content-wrapper .content .modal-dismiss {
	top: 12px;
	right: 12px;
}
.vue-winwheel #modalSpinwheel.custom-modal .content-wrapper .content .modal-dismiss i.icon_close {
	font-size: 30px;
	color: #da2a52;
}
.vue-winwheel canvas#canvas {
	position: relative;
}
.vue-winwheel .canvas-wrapper {
	position: relative;
}
.vue-winwheel .canvas-wrapper:after {
	content: '';
	display: block;
	width: 42px;
	background: #c4376f;
	height: 42px;
	position: absolute;
	left: calc(50% - 25px);
	margin: auto;
	border-radius: 100%;
	top: calc(50% - 29px);
	border: 5px solid white;
	box-sizing: content-box;
}
.vue-winwheel .canvas-wrapper:before {
	content: '';
	display: block;
	width: 310px;
	background: #0f0f0f;
	height: 310px;
	position: absolute;
	left: 0;
	right: 0;
	margin: 0 auto;
	border-radius: 100%;
	top: 0;
}
.vue-winwheel .wheel-wrapper {
	position: relative;
}
.vue-winwheel .wheel-wrapper:before {
	content: '';
	width: 62px;
	height: 47px;
	position: absolute;
	top: -10px;
	left: calc(50% - 31px);
	right: 0;
	display: block;
	z-index: 99999;
	background-image: url('./spinner-marker.svg');
	background-repeat: no-repeat;
	background-size: contain;
	background-position: center;
}
.vue-winwheel .wheel-wrapper .button-wrapper {
	margin: 0 auto;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	width: 231px;
	height: 118px;
}
.vue-winwheel .wheel-wrapper .btn.btn-play {
	padding: 0 58px !important;
	background: #00A651;
	height: 40px;
	line-height: 40px;
	color: white;
	font-weight: bold;
	text-decoration: none;
	border-radius: 2px;
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	letter-spacing: 2px;
}

.input-coupon {
  font-size: 24px;
  padding: 8px 16px;
  text-align: center;
  border: none;
  outline: none;
}

.input-coupon::selection {
  background: #fff;
  font-weight: bold;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-transform: uppercase;
}

.copy-coupon-button {
	padding: 0 58px !important;
	background: #00A651;
	height: 40px;
	line-height: 40px;
	color: white;
	font-weight: bold;
	border-radius: 2px;
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	letter-spacing: 2px;
  text-transform: uppercase;
}
</style>

```

e a props foi modificado para o seguinte contrato
```typescript
interface itemProps {
	textFillStyle: string
	fillStyle: string
	text: string
    coupon: string
}
```

e no arquivo `src\components\Wheel.vue`
substituir para 
```vue
<template>
  <div>
    <h1>ROLETA BLACK FRIDAY</h1>
    <h2>Gire para receber um dos benefícios</h2>

    <VueWinwheel :segments="options" />
  </div>
</template>

<script>
import VueWinwheel from 'vue-winwheel/vue-winwheel.vue';
// https://gist.github.com/Isabelaweb/33fbfbb90b7d06a81987d6cf846dc7f1
export default {
  components: {
    VueWinwheel,
  },
  data() {
    return {
      options: [
        {
          textFillStyle: '#fff',
          fillStyle: '#F79110',
          text: 'Brinde Supresa',
          coupon: 'brinde',
        },
        {
          textFillStyle: '#fff',
          fillStyle: '#3A3A3A',
          text: '5% de\nDesconto a Prazo',
          coupon: 'prazo5',
        },
        {
          textFillStyle: '#fff',
          fillStyle: '#F79110',
          text: 'Frete Grátis',
          coupon: 'clientefrete',
        },
        {
          textFillStyle: '#fff',
          fillStyle: '#3A3A3A',
          text: '3% de\nDesconto a Vista',
          coupon: 'avista3',
        },
      ],
    };
  },
};
</script>

<style></style>

```
### Resultado atual
#### Antes de rodar
<img src="/assets/resultado-passo-1.png"/>
#### Após rodar
<img src="/assets/resultado-passo-2.png"/>

### Estado atual
- [x] quando o cupom sai e clicar no botão é copiado para area de transferencia
- [ ] adicionar feedback após copiar o cupom
- [ ] melhorar visual
