<script>
	import JsonString from '../components/json/types/json-string'
	import JsonUndefined from '../components/json/types/json-undefined'
	import JsonNumber from '../components/json/types/json-number'
	import JsonBoolean from '../components/json/types/json-boolean'
	import JsonObject from '../components/json/types/json-object'
	import JsonArray from '../components/json/types/json-array'
	import JsonFunction from '../components/json/types/json-function'
	export default {
		name: 'JsonBox',
		inject: ['expandDepth'],
		props: {
			value: {
				type: [Object, Array, String, Number, Boolean, Function],
				default: null
			},
			message: {
				type: String,
				default: ''
			},
			timestamp: {
				type: String,
				default: ''
			},
			level: {
				type: String,
				default: ''
			},
			keyName: {
				type: String,
				default: ''
			},
			sort: Boolean,
			depth: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				expand: true
			}
		},
		mounted() {
			this.expand = this.depth >= this.expandDepth ? false : true
		},
		methods: {
			toggle() {
				this.expand = !this.expand
				try {
					this.$el.dispatchEvent(new Event('resized'))
				} catch (e) {
					// handle IE not supporting Event constructor
					var evt = document.createEvent('Event')
					evt.initEvent('resized', true, false)
					this.$el.dispatchEvent(evt)
				}
			}
		},
		render (h) {
			let elements = []
			let dataType
			if (this.value === null || this.value === undefined) {
				dataType = JsonUndefined
			} else if (Array.isArray(this.value)) {
				dataType = JsonArray
			} else if (typeof this.value === 'object') {
				dataType = JsonObject
			} else if (typeof this.value === 'number') {
				dataType = JsonNumber
			} else if (typeof this.value === 'string') {
				dataType = JsonString
			} else if (typeof this.value === 'boolean') {
				dataType = JsonBoolean
			} else if (typeof this.value === 'function') {
				dataType = JsonFunction
			}
			if (this.keyName && (this.value && (Array.isArray(this.value) || typeof this.value === 'object'))) {
				elements.push(h('span', {
					class: {
						'jv-toggle': true,
						open: !!this.expand
					},
					on: {
						click: this.toggle
					}
				}))
			}
			if (this.keyName) {
				elements.push(h('span', {
					class: {
						'jv-key': true
					},
					domProps: {
						innerHTML: `${this.keyName}:`
					}
				}))
			}
			elements.push(h(dataType, {
				class: {
					'jv-push': true
				},
				props: {
					jsonValue: this.value,
					keyName: this.keyName,
					sort: this.sort,
					depth: this.depth,
					expand: this.expand,
                    level: this.level,
                    message: this.message,
                    timestamp: this.timestamp
				},
				on: {
					'update:expand': value => {
						this.expand = value
					}
				}
			}))
			return h('div', {
				class: {
					'jv-node': true
				}
			}, elements)
		}
	}
</script>

<style lang="scss">
    .jv-node {
        position: relative;
        &:after {
            content: ','
        }
        &:last-of-type {
            &:after {
                content: ''
            }
        }
        & .jv-node {
            margin-left: 25px;
        }
    }
</style>