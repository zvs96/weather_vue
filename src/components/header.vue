<template>
    
    <div class="headerPanel">
        <img alt="Vue logo" src="../assets/logo.png">
        <form action="" id="searchArea" ref="mySearch"  >
            <input type="text" ref="cityValue" placeholder="Search City..." @focus="setFocus()" @focusout="delFocus()">
            <button type="submit" @click="preventSend($event)"><span class="lnr lnr-magnifier"></span></button>
        </form>
    </div>    
</template>
<script>
    export default {
        name:"Header",
        data(){
            return {
                city:""
            }
        },
        props:{

        },
        methods: {
            preventSend(event){
                this.city = this.$refs.cityValue.value;
                this.$emit('sendCity', this.city)
                event.preventDefault()
            },
            setFocus() {
                this.$refs.mySearch.classList.add("materialInput");
            },
            delFocus(){
                this.$refs.mySearch.classList.remove("materialInput");
            }
        }
    }
</script>
<style lang="scss">
    .headerPanel{
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 0 40px;
        background-color: #525050;
        & > img{
            max-width: 140px;
            padding: 10px;
        }
        .materialInput{
            &:after{
                width: 100% !important;
            }
        }
        #searchArea{
            &:after{
                content: "";
                bottom: 0;
                position: absolute;
                left: 0;
                right: 0;
                margin: auto;
                width: 0px;
                background-color: #fff;
                height: 1px;   
                transition: .4s;             
            }
            position: relative;
            padding-right: 0px;
            ::-webkit-input-placeholder{
                color: #fff
            }
            input{
                background-color: transparent;
                border: none;
                padding: 5px;
                outline: none;
                box-shadow: 0;
                color: #fff;
            }
            button{
                position: absolute;
                right: 5px;
                top: 0;
                bottom: 0;
                margin: auto;
                background: none;
                border: none;
                color: #fff;
                cursor: pointer;
                outline: none;
                box-shadow: 0;
            }
        }
    }
</style>


