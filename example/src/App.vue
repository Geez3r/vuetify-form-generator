<template>
	<v-app>
		<v-container fluid>
			<v-layout>
				<v-flex xs12>
					<v-tabs v-model="active">
						<v-tab ripple key="form">Form</v-tab>
						<v-tab ripple key="model">Model</v-tab>
						<v-tab ripple key="schema">Schema</v-tab>
						<v-tab-item key="form">
							<v-card flat>
								<v-card-text>
									<v-form-generator 
										:model="model" 
										:schema="schema" 
										:options="options"
										@blur="onBlur"
										@change="onChange"
										@focus="onFocus"
										@input="onInput"/>
								</v-card-text>
							</v-card>
						</v-tab-item>
						<v-tab-item key="model">
							<v-card flat>
								<v-card-text>
									<pre>{{ model }}</pre>
								</v-card-text>
							</v-card>
						</v-tab-item>
						<v-tab-item key="schema">
							<v-card flat>
								<v-card-text>
									<pre>{{ schema }}</pre>
								</v-card-text>
							</v-card>
						</v-tab-item>
					</v-tabs>
				</v-flex>
			</v-layout>
		</v-container>
	</v-app>
</template>

<script>
	export default {
		components: {
			'v-form-generator': require('vuetify-form-generator').default
		},
		data(){
			return {
				model: {
					active: null,
					id: 1,
					name: "John Doe",
					password: "J0hnD03!x4",
					skills: ["Javascript", "VueJS"],
					email: "john.doe@gmail.com",
					status: true,
					usePassword: true,
					showHiddenField: true,
					skillsRadio: null,
					switch: false,
					age: 30,
					mvrStatus: '',
					mvrDate: new Date().toISOString().substr(0, 10),
					menu2: false,
					firstName: '',
					lastName: '',
					driverPanel: [true]
				},
				schema: {
					items: [
						{
							category: "field",
							type: "number",
							label: "ID (disabled text field)",
							model: "id",
							readonly: true, 
							disabled: true,
							visible: true
						},{
							category: "field",
							type: "number",
							label: "Hidden Field",
							model: "idHidden",
							readonly: true, 
							disabled: true,
							visible: false
						},{
							category: "field",
							type: "text",
							label: "Name",
							model: "name",
							placeholder: "Your name",
							featured: true,
							required: true
						},{
							category: "field",
							type: "number",
							label: "Age",
							model: "age",
						},{
							category: "field",
							type: "checkbox",
							label: "Use password?",
							model: "usePassword"
						}, {
							category: "field",
							type: "password",
							label: "Password",
							model: "password",
							disabled: false,
							min: 6,
							required: true,
							hint: "Minimum 6 characters",
							passwordVisible: false
						},{
							category: "field",
							type: "select",
							label: "Skills",
							model: "skills",
							values: ["Javascript", "VueJS", "CSS3", "HTML5"]
						},{
							category: "field",
							type: "radio",
							label: "Select a skill",
							model: "skillsRadio",
							values: [
								{
									"label": "Javascripts",
									"value": "1"
								},
								{
									"label": "VueJS",
									"value": "2"
								},
								{
									"label": "CSS3",
									"value": "3"
								}
							]
						},
						{
							category: "field",
							type: "switch",
							label: "Email field is disabled",
							model: "switch"
						},
						{
							category: "field",
							type: "email",
							label: "E-mail",
							model: "email",
							placeholder: "User's e-mail address",
							disabled: (model, field) => model.switch === true
						},{
							category: "field",
							type: "checkbox",
							label: "Status",
							model: "status",
							default: true
						},{
							category: "field",
							type: "checkbox",
							label: "Show next field?",
							model: "showHiddenField"
						},{
							category: "field",
							type: "text",
							label: "Hidden Field",
							model: "conditionlHiddenField",
							placeholder: "Some text",
							visible: "(model, field) => model.showHiddenField === true",
						},{
							category: "group",
							type: "expansionpanels",
							model: "driverPanel",
							panels: [
								{
									key: "driverOne",
									label: (model, field) => `Driver 1 ${model.firstName} ${model.lastName}`,
									items: [
										{
											category: "field",
											type: "text",
											label: "First Name",
											seperateLabel: true,
											model: "firstName",
										},
										{
											category: "field",
											type: "text",
											label: "Last Name",
											seperateLabel: true,
											model: "lastName",
										}
									]
								}
							]
						},{
							category: "group",
							type: "cards",
							cards: [
								{
									key: "policyDetails",
									label: "Policy Details",
									items: [
										{
											category: "field",
											type: "select",
											label: "Whats the status of the MVR report?",
											seperateLabel: true,
											hint: "Heres a persistentHint = true. Select Received for a surprise!",
											persistentHint: true,
											singleLine: true,
											clearable: true,
											model: "mvrStatus",
											values: ["Not ordered", "Ordered", "Received"]
										},
										{
											category: "field",
											type: "datepicker",
											label: "MVR Ordered Date",
											seperateLabel: true,
											model: "mvrDate",
											visible: (model, field) => model.mvrStatus == 'Received'
										}
									]
								}
							]
						},{
							category: "group",
							type: "cards",
							cards: [
								{
									key: "locationDetails",
									label: "Location Details",
									items: [
										{
											category: "group",
											type: "tabs",
											tabs: [
												{
													key: "location1Details",
													label: "Location 1",
													items: [
														{
															category: "field",
															type: "textarea",
															id: "myStory",
															label: "My story",
															model: "story"
														},
														{
															category: "field",
															type: "email",
															id: "email",
															label: "Email Address",
															model: "email"
														}
													]
												},
												{
													key: "location2Details",
													label: "Location 2",
													items: [
														{
															category: "field",
															type: "select",
															label: "Color",
															model: "color",
															values: [
																"Red",
																"Green",
																"Blue"
															]
														},
														{
															category: "field",
															type: "number",
															id: "timeout",
															label: "Timeout in Seconds",
															model: "timeout"
														}
													]
												}
											]
										}
									]
								}
							]
						}
					]
				},
				
				options: {

				}
			}
		},
		methods: {
			onBlur: function(){
				console.info('blur')
			},
			onChange: function(evt){
				console.info('change')
				this.model[evt.model] = evt.value
			},
			onFocus: function(){
				console.info('focus')
			},
			onInput: function(evt){
				console.info('input')
				this.model[evt.model] = evt.value
			}
		}
	}
</script>
