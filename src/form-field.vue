<template>
	<div>
		<div v-if="fieldVisible(field)">
			<div v-if="field.type == 'email'">
				<v-text-field
						v-model="localValue"
						:label="field.label"
						:required="field.required"
						:readonly="field.readonly"
						:disabled="field.disabled"
						:placeholder="field.placeholder"
						:rules="validationRules.email"
						@blur="onBlur"
						@change="onChange"
						@focus="onFocus"
						@input="onInput"
					></v-text-field>
			</div>

			<div v-else-if="field.type == 'password'">
				<v-text-field
						v-model="localValue"
						:label="field.label"
						:required="field.required"
						:readonly="field.readonly"
						:disabled="field.disabled"
						:placeholder="field.placeholder"
						:append-icon="field.passwordVisible ? 'visibility_off' : 'visibility'"
								:type="field.passwordVisible ? 'text' : 'password'"
						@blur="onBlur"
						@change="onChange"
						@focus="onFocus"
						@input="onInput"              
					></v-text-field>
			</div>

			<div v-else-if="field.type == 'number'">
				<v-text-field
					v-model="localValue"
					type="number"
					:label="field.label"
					:required="field.required"
					:readonly="field.readonly"
					:disabled="field.disabled"
					:placeholder="field.placeholder"
					@blur="onBlur"
					@change="onChange"
					@focus="onFocus"
					@input="onInput"
				></v-text-field>
			</div>

					<div v-else-if="field.type == 'datepicker'">
						<v-menu
							v-model="menuDatePicker"
							:close-on-content-click="false"
							:nudge-right="40"
							lazy
							transition="scale-transition"
							offset-y
							full-width
							min-width="290px"
						>
							<template v-slot:activator="{ on }">
								<v-text-field
									v-model="localValue"
									:label="showLabel(field)"
									prepend-icon="event"
									readonly
									v-on="on"
								></v-text-field>
							</template>
							<v-date-picker v-model="localValue" @input="menuDatePicker = false"></v-date-picker>
						</v-menu>
					</div>

			<div v-else-if="field.type == 'select'">
					<v-select
						v-model="localValue"
						:items="field.values"
						:label="field.label"
						:required="field.required"
						:readonly="field.readonly"
						:disabled="field.disabled"
						:placeholder="field.placeholder"
						:clearable="field.clearable"
						:multiple="field.multiple"
							:hint="field.hint"
							:persistent-hint="field.persistentHint"
							:single-line="field.singleLine ? true : false"
							menu-props="bottom"
						@change="onChange"
					></v-select>
			</div>

			<div v-else-if="field.type == 'radio'">
				<v-container fluid>
					<p>{{ field.label }}</p>
					<v-radio-group v-model="localValue">
						<v-radio
							v-for="n in field.values"
							:key="n.value"
							:label="n.label"
							:value="n.value"
							:disabled="field.disabled"
							:readonly="field.readonly"
							@change="onChange"
						></v-radio>
					</v-radio-group>
				</v-container>
			</div>

			<div v-else-if="field.type == 'switch'">
				<v-switch v-model="localValue"
					:label="field.label"
					:value="field.value"
					:disabled="field.disabled"
					:readonly="field.readonly"
					@change="onChange"
				></v-switch>
			</div>


			<div v-else-if="field.type == 'checkbox'">
					<v-checkbox
						v-model="localValue"
						:label="field.label"
						:required="field.required"
						:disabled="field.disabled"
						@change="onChange"
					></v-checkbox>
			</div>

			<div v-else-if="field.type == 'textarea'">
				<v-text-field
						v-model="localValue"
						:label="field.label"
						:required="field.required"
						:readonly="field.readonly"
						:disabled="field.disabled"
						:placeholder="field.placeholder"
						multi-line
						v-bind:textarea="field.featured"
						@blur="onBlur"
						@change="onChange"
						@focus="onFocus"
						@input="onInput"		      
					></v-text-field>
			</div>

			<div v-else>
				<v-text-field
						v-model="localValue"
						:label="field.label"
						:required="field.required"
						:readonly="field.readonly"
						:disabled="field.disabled"
						:placeholder="field.placeholder"
					:counter="field.counter"
					:hint="field.hint"
						@blur="onBlur"
						@change="onChange"
						@focus="onFocus"
						@input="onInput"			  
					></v-text-field>


					<v-alert v-if="field.type != 'text'" color="error" icon="warning" value="true">
						<strong>The {{field.type}} type is not yet implemented.</strong> <br>
						{{field}}
					</v-alert>
					
			</div>
		</div>
	</div>
</template>

<script>
	import { isFunction, isNil } from "lodash";
	export default{
		name: 'v-form-generator-field',
		props: {
			field: Object,
			value: null,
			model: Object
		},
		data(){
			return {
				localValue: this.value,
				validationRules: {
					email: [
						(v) => /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(v) || this.validationErrorMessages.emailInvalid
					]
				},
				validationErrorMessages:{
					'emailInvalid': 'E-mail must be valid'
				}
			}
		},
		created: function () {
			// On load
		},
		methods: {
			onBlur: function(){
				this.$emit('blur')
			},
			onChange: function(evt){
				this.$emit('change', { model: this.field.model, value: evt })
			},
			onFocus: function(){
				this.$emit('focus')
			},
			onInput: function(evt){
				this.$emit('input', { model: this.field.model, value: evt })
			},
			
			appendPasswordIconCheckbox(){
				return () => this.field.passwordVisible = !this.field.passwordVisible
			},

			// Get visible prop of field
			fieldVisible(field) {
				if (isFunction(field.visible)) return field.visible.call(this, this.model, this.field)

				if (isNil(field.visible)) return true;

				return field.visible;
			}
		}
	}
</script>