<template>
    <Page class="page">
        <ActionBar class="action-bar">
            <!-- 
            Use the NavigationButton as a side-drawer button in Android
            because ActionItems are shown on the right side of the ActionBar
            -->
            <NavigationButton ios:visibility="collapsed" icon="res://menu" @tap="onDrawerButtonTap"></NavigationButton>
            <!-- 
            Use the ActionItem for IOS with position set to left. Using the
            NavigationButton as a side-drawer button in iOS is not possible,
            because its function is to always navigate back in the application.
            -->
            <ActionItem 
                android:visibility="collapsed" 
                @tap="onDrawerButtonTap"
                ios.position="left">
            </ActionItem>
            <Label class="action-bar-title" text="Home"></Label>
        </ActionBar>

        <GridLayout class="page-content" rows="*,*,*">
            <Label :text="score" row="0" :class="score > 0 ? 'goodScoreLabel' : score < 0 ? 'badScoreLabel' : 'zeroScoreLabel'"/>
            <Button text="Add Good Shit" @tap="addGoodShit" row="1" class="goodButton"/>
            <Button text="Add Bad Shit" @tap="addBadShit" row="2" class="badButton"/>
            <!-- <Label class="page-icon fa" text.decode="&#xf015;"></Label>
            <Label class="page-placeholder" :text="message"></Label> -->
        </GridLayout>

    </Page>
</template>

<script>
    import * as utils from "~/shared/utils";
    import SelectedPageService from "../shared/selected-page-service";
    import AddGoodStuff from './AddGoodStuff';
    const appSettings = require("tns-core-modules/application-settings");

    export default {
        data(){
            return{
                score: 0
            }
        },
        mounted() {
            SelectedPageService.getInstance().updateSelectedPage("Home");
            var data = appSettings.getString("achievements", "0");
            if(data != "0"){
                var x = JSON.parse(data);
                this.score = x[x.length-1].total;
            }else{
                this.score = "0";
            }
        },
        computed: {
            message() {
                return "<!-- Page content goes here -->";
            }
        },
        methods: {
            onDrawerButtonTap() {
                utils.showDrawer();
            },
            addGoodShit(){
                this.$navigateTo(AddGoodStuff,{
                    props: {
                        type: 'good'
                    }
                });
            },
            addBadShit(){
                this.$navigateTo(AddGoodStuff,{
                    props: {
                        type: 'bad'
                    }
                });
            }
        }
    };
</script>

<style scoped lang="scss">
    // Start custom common variables
    @import '../app-variables';
    // End custom common variables
    .goodScoreLabel{
        color: white;
        text-align: center;
        font-weight: bold;
        font-size: 40em;
        border-radius: 450px;
        width: 27%;
        height: 300px;
        background-color: green;
    }
    .badScoreLabel{
        color: white;
        text-align: center;
        font-weight: bold;
        font-size: 40em;
        border-radius: 450px;
        width: 27%;
        height: 300px;
        background-color: red;
    }
    .zeroScoreLabel{
        color: white;
        text-align: center;
        font-weight: bold;
        font-size: 40em;
        border-radius: 450px;
        width: 27%;
        height: 300px;
        background-color: black;
    }
    .goodButton{
        height: 200px;
        width: 80%;
        border-radius: 20px;
        background-color: green;
        font-weight: bold;
        color: white;
    }
    .badButton{
        height: 200px;
        width: 80%;
        border-radius: 20px;
        background-color: red;
        font-weight: bold;
        color: white;
    }
    // Custom styles
</style>