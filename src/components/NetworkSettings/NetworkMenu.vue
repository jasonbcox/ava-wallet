<template>
    <div class="network_menu" :connected="status==='connected'">
        <div class="toggle_but" @click="toggleMenu">
            <img src="@/assets/network_off.png" v-if="status==='disconnected' || status==='connecting'">
            <img src="@/assets/network_on.png" v-else>
            <button v-if="status==='connected'">{{activeNetwork.name}}</button>
            <button v-else-if="status==='connecting'">Connecting...</button>
            <button v-else>Disconnected</button>
        </div>
        <transition name="fade">
            <div class="network_body" v-if="isActive">
                <div class="header">
                    <template v-if="page==='list'">
                        <h4>Networks</h4>
                        <button @click="viewCustom">Add Custom</button>
<!--                        <button @click="closeMenu" style="background-color: transparent;-->
<!-- color: #999;"><fa icon="times"></fa></button>-->
                    </template>
                    <template v-if="page==='custom'">
                        <h4>Add Custom Network</h4>
                        <button @click="viewList" style="background-color: transparent; color: #3a3144">Cancel</button>
                    </template>
                    <template v-if="page==='edit'">
                        <h4>Edit Network</h4>
                        <button @click="viewList" style="background-color: transparent; color: #3a3144">Cancel</button>
                    </template>
                </div>

                <transition name="fade" mode="out-in">
                    <ListPage v-if="page==='list'"></ListPage>
                    <CustomPage v-if="page==='custom'" @add="addCustomNetwork"></CustomPage>
                    <EditPage v-if="page==='edit'" :net="editNetwork" ></EditPage>
                </transition>

            </div>
        </transition>
    </div>
</template>
<script>
    import NetworkRow from './NetworkRow';
    import CustomPage from './CustomPage';
    import ListPage from './ListPage';
    import EditPage from "@/components/NetworkSettings/EditPage";
    export default {
        components: {
            ListPage,
            NetworkRow,
            CustomPage,
            EditPage
        },
        data(){
            return{
                page: 'list', // list | custom | edit
                isActive: false,
                editNetwork: null,
            }
        },
        methods: {
            viewCustom(){
                this.page = 'custom';
            },
            viewList(){
                this.page = 'list';
            },
            closeMenu(){
                this.isActive = false;
            },
            toggleMenu(){
                this.isActive = !this.isActive;

                // if(this.isActive){
                //
                // }
            },
            addCustomNetwork(data){
                this.$store.commit('Network/addNetwork', data);
                this.page = 'list';
            },
            onedit(network){
                this.editNetwork = network;
                this.page = 'edit';
            }
        },
        computed: {
            status(){
                return this.$store.state.Network.status;
            },
            // isConnecting(){
            //     return this.activeNetwork!==null && !this.$store.state.Network.isConnected;
            // },
            // isConnected(){
            //     return this.activeNetwork!==null && this.$store.state.Network.isConnected;
            // },
            activeNetwork(){
                return this.$store.state.Network.selectedNetwork;
            },
            networks(){
                return this.$store.state.Network.networks;
            }
        }
    }
</script>
<style scoped lang="scss">
    @use '../../main';

    .network_menu{
        position: relative;
    }

    .toggle_but{
        display: flex;
        color: #C4C4C4;
        min-width: 140px;
        margin-right: 30px;
        img{
            max-height: 24px;
            object-fit: contain;
            margin-right: 5px;
        }
    }

    .network_body{
        position: fixed;
        top: 60px;
        right: 15vw;;
        border: 1px solid #999;
        border-radius: 4px;
        width: 340px;
        background-color: #fff;
        /*transform: translateY(100%);*/
        box-shadow: 2px 2px 10px rgba(0,0,0,0.4);
    }

    .header{
        border-bottom: 1px solid #EAEDF4;
        padding: 10px 15px;
        display: flex;
        h4{
            flex-grow: 1;
        }

        button{
            background-color: #2960CD;
            color: #fff;
            font-size: 12px;
            padding: 3px 14px;
            border-radius: 4px;
        }
    }


    .network_menu[connected]{
        .toggle_but{
            color: #2960CD;
        }
    }


    @media only screen and (max-width: main.$mobile_width) {

        .network_body{
            position: fixed;
            width: 100vw;
            z-index: 2;
            right: 0 !important;
            left: 0 !important;
        }
    }



    @include main.medium-device {

        .toggle_but{
            min-width: auto;
        }
    }
</style>
