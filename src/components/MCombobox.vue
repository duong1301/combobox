<template>
    <div v-bind:class="{active:isActive}" class="m-combobox">
        <div class="m-combobox__selection">
            <input 
                :name="propName"
                v-model="textValue"
                ref="input" 
                @focusout="onFocustOutHandle" 
                @keydown="onInputKeyDownHandle"
                @input="onInputHandle"
            type="text">

            <div @mousedown="onToggleClick" class="toggle">
                <div class="chevron--down"></div>
            </div>
        </div>

        <div v-show="isActive" class="m-combobox__menu">
            <div 
                v-for="(item,index) in data" :key="index"
                @mousedown="onItemClick(item, index)"
                class="item"
                v-bind:class="{
                    selected: item[propVal] === entitySelected?.[propVal],
                    'key-hover': index === this.index
                }"
            >
                {{ item[propLabel] }}
                
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props:["propName","propVal","propLabel","dataSource","value"],
    data() {
        return {
            isActive:false,
            textValue:"",
            data:[],
            
            entitySelected:null,
            enum:{
                keyCode:{
                    enter:13,
                    arrowLeft:37,
                    arrowUp:38,
                    arrowRight:39,
                    arrowDown:40,
                }
            },
            index:-1
        }
    },

    created(){
        this.data = this.dataSource;
        
        if(this.value != null){
            this.data.forEach(element => {
                
                if(element[this.propVal] === this.value){

                    this.entitySelected = element;
                    this.textValue = element[this.propLabel];
                }
                
            });
        }
    },

    
    methods: {
        showMenu:function(){
            this.isActive = true;
        },

        hideMenu:function(){
            this.isActive = false;
        },
        onToggleClick:function(e){
            e.preventDefault();
            
            this.isActive= !this.isActive;
            this.$refs.input.focus();
        },
        onItemClick:function(entity, index){
            this.entitySelected = entity;
            this.index = index;
            this.textValue = entity[this.propLabel];
        },

        onInputKeyDownHandle:function(e){
            let keyCode = e.which;
            switch (keyCode) {
                case this.enum.keyCode.arrowDown:
                    if(this.isActive === false)
                        this.isActive = true;
                    else{
                        let menuLength = this.data.length;
                        if(this.index != menuLength - 1){
                            this.index++;
                        }
                        else{
                            this.index = 0;
                        }
                        
                    }
                    break;
                case this.enum.keyCode.arrowUp:
                    if(this.isActive === true){
                        let menuLength = this.data.length;
                        if(this.index === 0){
                            this.index = menuLength - 1;
                        }
                        else{
                            this.index--;
                        }
                    }
                    break;

                case this.enum.keyCode.enter:
                    if(this.isActive){
                        this.entitySelected = this.data[this.index];
                        this.textValue = this.entitySelected[this.propLabel];
                        this.isActive = false;
                        this.data = this.dataSource;
                    }
                    break;

                default:
                    
                    break;
            }

            
        },
        onInputHandle:function(e){
            this.showMenu();
            let value = e.target.value;
            
            this.data = this.dataSource.filter(function(item){
                return item[this.propLabel].includes(value);
            })
            
            
        },

        onFocustOutHandle:function(){
            this.hideMenu();
            let text = this.$refs.input.value;
            let texts = this.data.map((item)=>{
                return item[this.propLabel];
            })

            if(texts.indexOf(text) === -1){
                this.entitySelected = null;
                this.index = -1;
            }
        }
    },

    watch: {
        
        data:function(){  
            
            let code = this.data.map((item)=>{
                return item[this.propVal];
            })
            this.index = code.indexOf(this.entitySelected?.[this.propVal]);
        },

        entitySelected:function(){
            this.$emit("update:value", this.entitySelected?.[this.propVal])
        }
    },
}
</script>

<style>
    .chevron--down{
        width: 0;
        height: 0;
        border-left: 6px transparent solid;
        border-right: 6px transparent solid;
        border-top: 6px black solid;
    }

    .m-combobox{
        position: relative;
        background-color: #fff;
        
    }

    .m-combobox__selection{
        position: relative;
        width: 100%;
        display: flex;
    }
    .m-combobox input{
        height: 36px;
        width: 100%;
                
    }
    .m-combobox .toggle{
        position: absolute;
        right: 0;
        padding: 12px;
        top: 50%;
        transform: translateY(-50%);        
    }
    .m-combobox .m-combobox__menu{
        position: absolute;
        top: 100%;
        width: 100%;
        background-color: #fff;
    }
    .m-combobox .item{
        padding: 12px;
        border: 1px solid #ccc;
    }
    .m-combobox .item.selected{
        background-color: #333;
    }
    .m-combobox .item:hover{
        background-color: #ddd;
    }
    .m-combobox .item.key-hover{
        background-color: #aaa;
    }

</style>