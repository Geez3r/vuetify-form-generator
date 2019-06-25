<template>
    <div id="form-generator">
        <div v-for="(schemaItem, schemaItemIndex) in schema">
            <v-flex mb-2>
                <div v-if="schemaItemIndex == 'items'">
                    <div v-for="item in schemaItem">
                        <!-- Field -->
                        <div v-if="item.category == 'field'">
                            <v-form-generator-field 
                                :field="item" 
                                :value="model[item.model]"
                                :model="localModel"
                                @blur="onBlur"
                                @change="onChange"
                                @focus="onFocus"
                                @input="onInput">
                            </v-form-generator-field>
                        </div>

                        <!-- or Group -->
                        <div v-else-if="item.category == 'group'">
                            <!-- TABS -->
                            <div v-if="item.type == 'tabs'">
                                <v-tabs>
                                    <v-tab
                                        v-for="tab in item.tabs"
                                        :key="tab.key"
                                        ripple
                                    >
                                        {{tab.label}}
                                    </v-tab>
                                    <v-tab-item
                                        v-for="tab in item.tabs"
                                        :key="tab.key"
                                    >
                                        <v-card flat>
                                            <v-card-text>
                                                <div v-for="subitem in tab.items" :key="subitem.id">
                                                    <div v-if="subitem.category == 'field'">
                                                        <v-form-generator-field 
                                                            :field="subitem" 
                                                            :value="model[subitem.model]"
                                                            :model="localModel"
                                                            @blur="onBlur"
                                                            @change="onChange"
                                                            @focus="onFocus"
                                                            @input="onInput">
                                                        </v-form-generator-field>
                                                    </div>
                                                    
                                                    <!-- Group within a group -->
                                                    <div v-else-if="subitem.category == 'group'">
                                                        <v-form-generator 
                                                            :model="localModel" 
                                                            :schema="{ items: [subitem] }" 
                                                            :options="options"
                                                            @blur="onBlur"
                                                            @change="onChange"
                                                            @focus="onFocus"
                                                            @input="onInput">
                                                        </v-form-generator>
                                                    </div>
                                                </div>
                                            </v-card-text>
                                        </v-card>
                                    </v-tab-item>
                                </v-tabs>
                            </div>

                            <!-- CARDS -->
                            <div v-else-if="item.type == 'cards'">
                                <v-card v-for="card in item.cards" :key="card.key">
                                    <v-toolbar
                                    flat
                                    color="blue-grey"
                                    dark
                                    >
                                    <v-toolbar-title>{{ groupLabel(card) }}</v-toolbar-title>
                                    </v-toolbar>

                                    <v-card-text>
                                        <div v-for="(subitem, i) in card.items" :key="i">
                                            <div v-if="subitem.category == 'field'">
                                                <v-form-generator-field 
                                                    :field="subitem" 
                                                    :value="model[subitem.model]"
                                                    :model="localModel"
                                                    @blur="onBlur"
                                                    @change="onChange"
                                                    @focus="onFocus"
                                                    @input="onInput">
                                                </v-form-generator-field>
                                            </div>

                                            <!-- Group within a group -->
                                            <div v-else-if="subitem.category == 'group'">
                                                <v-form-generator 
                                                    :model="localModel" 
                                                    :schema="{ items: [subitem] }" 
                                                    :options="options"
                                                    @blur="onBlur"
                                                    @change="onChange"
                                                    @focus="onFocus"
                                                    @input="onInput">
                                                </v-form-generator>
                                            </div>
                                            
                                        </div>
                                    </v-card-text>
                                    <v-divider></v-divider>
                                    <!-- NOT USED -->
                                    <!-- <v-card-actions>
                                        <v-spacer></v-spacer>
                                        <v-btn
                                            color="success"
                                            depressed
                                        >
                                            Post
                                        </v-btn>
                                    </v-card-actions> -->
                                </v-card>
                            </div>

                            <!-- EXPANSION PANELS -->
                            <div v-else-if="item.type == 'expansionpanels'">
                                <v-expansion-panel v-model="item.model">
                                    <v-expansion-panel-content v-for="panel in item.panels" :key="panel.key">
                                        <template v-slot:header>
                                            <div>{{ groupLabel(panel) }}</div>
                                        </template>
                                        <v-card>
                                            <v-card-text>
                                                <div v-for="subitem in panel.items" :key="subitem.id">
                                                    <div v-if="subitem.category == 'field'">
                                                        <v-form-generator-field 
                                                            :field="subitem" 
                                                            :value="model[subitem.model]"
                                                            :model="localModel"
                                                            @blur="onBlur"
                                                            @change="onChange"
                                                            @focus="onFocus"
                                                            @input="onInput">
                                                        </v-form-generator-field>
                                                    </div>
                                                </div>
                                            </v-card-text>
                                        </v-card>
                                    </v-expansion-panel-content>
                                </v-expansion-panel>
                            </div>
                        </div>
                    </div>
                </div>
            </v-flex>
        </div>
    </div>
</template>

<script>
    import { isFunction, isNil } from "lodash";
    export default{
        name: 'v-form-generator',
        props: {
            'model': Object,
            'schema': Object,
            'options': Object
        },
        components: {
            'v-form-generator-field': require('./form-field.vue').default
        },
        data(){
            return {
                localModel: this.model
            }
        },
        created: function () {
            // On load
        },
        methods: {
            updateModel: function(name, value){
                this.localModel[name] = value
            },
            groupLabel(field) {
				if (isFunction(field.label)) return field.label.call(this, this.model, this.field)

				return field.label;
			},
            onBlur: function(){
                console.info('blur')
                this.$emit('blur')
            },
            onChange: function(evt){
                console.info('change')
                this.updateModel(evt.model, evt.value)
                this.$emit('change', evt)
            },
            onFocus: function(){
                console.info('focus')
                this.$emit('focus')
            },
            onInput: function(evt){
                console.log('input')
                this.updateModel(evt.model, evt.value)
                this.$emit('input', evt)
            },
        }
    }
</script>