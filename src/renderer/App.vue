<template>
	<div id="app">
		<div id="display">
			<pre>{{ output + '\n' }}</pre>
		</div>
		<div id="input-line">
			<pre><span>></span>{{ input }}</pre>
			<pre class="cursor-container"><span>></span>{{ input }}<span class="cursor" :style="{color: cursorColor}">_</span></pre>
		</div>
	</div>
</template>

<script >

export default {
    name: 'vue-g',
	data() {
		return {
			name:  this.name,
			input: '',
			output: '',
			cursorColor: '#8c8c8c',
		}
	},
	methods: {
		logKeypress(event) {
			let {key} = event
			let specialMethod = this.specialKeys[key]
			if (specialMethod) {
				specialMethod()
				return
			}
			if (!this.ignored.includes(key)) {
				if (this.input.length >= this.maxChars) {
					return
				}
				this.input += key
			}
		},
	},
	mounted() {
		let textColor = this.cursorColor
		this.charHeight = 36
		this.charWidth = 18
		this.maxChars = Math.floor(window.innerWidth / this.charWidth) - 2
		this.maxLines = Math.floor(window.innerHeight / this.charHeight) - 2

		// Match all lines up to the max number
		let lineMatch = new RegExp(`^[\\s\\S]*((.*\\s.*){${this.maxLines}})$`)

		this.ignored = [
			'Meta',
			'Shift',
			'Alt',
			'Control',
			'Enter',
			'Tab',
			'Backspace',
			'CapsLock',
			'ArrowLeft',
			'ArrowRight',
			'ArrowUp',
			'ArrowDown',
			'Escape',
		]
		this.specialKeys = {
			Enter: () => {
				let output = `${this.output}\n>${this.input}`
				let match = output.match(lineMatch)
				if (match) {
					output = match[1]
				}
				this.output = output + '\n'
				this.input = ''
			},
			Backspace: () => {
				this.input = this.input.slice(0, -1)
			},
			Tab: () => {
				this.input += '    '
			},
		}

		window.addEventListener('keyup', this.logKeypress)

		setInterval(() => {
			this.cursorColor = this.cursorColor === textColor ? 'transparent' : textColor;
		},
		120)
	},
  }
</script>

<style lang="scss">

* {
	box-sizing: border-box;
	margin: 0;
	padding: 0;
}

body, pre {
	font-family: 'Px437 IBM VGA8';
	margin: 0 ;
}

#app {
	font-family: 'Terminal', monospace;
	color: #8c8c8c;
	background: #000000;
	width: 100vw;
	height: 100vh;
	font-size: 36px;
	display: flex;
	flex-direction: column;
}

#display {
	width: 100%;;
	/* background-color: #00ffcb; */
	flex-grow: 2;
	display: flex;
	justify-content: flex-end;
	flex-direction: column;
}

#input-line {
	width: 100%;
	position: relative;
	/* background-color: #003b6f; */
}

.cursor-container {
	position: absolute;
	top: 0;
	height: 36px;
	width: 100%;
	color: transparent;
}

.cursor {
	color: #8c8c8c;
}
</style>
