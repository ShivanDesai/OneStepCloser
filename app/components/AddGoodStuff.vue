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
            <Label class="action-bar-title" :text="$props.type=='good' ? 'Add Accomplishments' : 'Add Vices'"></Label>
        </ActionBar>

        <GridLayout class="page-content" rows="*,*,*">
            <TextField v-model="title" hint="title of accomplishment" row="0" class="title"/>
            <ListPicker :items="points" v-model="selectedPoint" selectedIndex="0" row="1"/>
            <Button :text="$props.type=='good' ? 'Add Accomplishment' : 'Add Vice'" @tap="addGoodShit" row="2" :class="$props.type=='good' ? 'goodButton' : 'badButton'"/>
        </GridLayout>

    </Page>
</template>

<script>
    import * as utils from "~/shared/utils";
    import SelectedPageService from "../shared/selected-page-service";
    const appSettings = require("tns-core-modules/application-settings");
    import Home from './Home';

    export default {
        props: ['type'],
        data(){
            return{
                points: [1,2,3,4,5,6,7],
                selectedPoint: 0,
                title: ""
            };
        },
        mounted() {
            SelectedPageService.getInstance().updateSelectedPage("Home");
        },
        // computed: {
        //     message() {
        //         return "<!-- Page content goes here -->";
        //     }
        // },
        methods: {
            onDrawerButtonTap() {
                utils.showDrawer();
            },
            addGoodShit(){
                console.log("-------COMING HERE----------");
                // appSettings.remove("achievements");
                var data = appSettings.getString("achievements", "nan");
                var d = new Date();
                var month = d.getMonth()+1;
                var day = d.getDate()+1;
                var str = d.getFullYear() + '-' + month + '-' + day;
                var hour = d.getHours();
                if(data == "nan"){
                    var x = [];
                    var o = {};
                    if(this.type == "good"){
                        o = {
                            date: str,
                            goodStuff: [{title: this.title, score: this.points[this.selectedPoint], time: hour}],
                            badStuff: [],
                            total: this.points[this.selectedPoint]
                        }
                    }
                    else{
                        o = {
                            date: str,
                            goodStuff: [],
                            badStuff: [{title: this.title, score: (this.points[this.selectedPoint] * -1), time: hour}],
                            total: (this.points[this.selectedPoint] * -1)
                        }
                    }
                    x.push(o);
                    appSettings.setString("achievements", JSON.stringify(x));
                }
                else{
                    var x = JSON.parse(data);
                    var o = x[x.length-1];
                    if(o.date == str){
                        if(this.type == "good"){
                            o.goodStuff.push({title: this.title, score: this.points[this.selectedPoint], time: hour});
                            o.total = o.total + this.points[this.selectedPoint];
                        }
                        else{
                            o.badStuff.push({title: this.title, score: (this.points[this.selectedPoint] * -1), time: hour});
                            o.total = o.total + (this.points[this.selectedPoint] * -1);
                        }
                        console.log("---------SCORE: " + o.total + "------------");
                        appSettings.setString("achievements", JSON.stringify(x));
                    }
                    else{
                        if(this.type == "good"){
                            o = {
                                date: str,
                                goodStuff: [{title: this.title, score: this.points[this.selectedPoint], time: hour}],
                                badStuff: [],
                                total: this.points[this.selectedPoint]
                            }
                        }
                        else{
                            o = {
                                date: str,
                                goodStuff: [],
                                badStuff: [{title: this.title, score: (this.points[this.selectedPoint] * -1), time: hour}],
                                total: (this.points[this.selectedPoint] * -1)
                            }
                        }
                        console.log("---------SCORE: " + o.total + "------------");
                        x.push(o);
                        appSettings.setString("achievements", JSON.stringify(x));
                    }
                }
                this.$navigateTo(Home, { clearHistory: true });
            }
        }
    };
</script>

<style scoped lang="scss">
    // Start custom common variables
    @import '../app-variables';
    // End custom common variables
    .title{
        color: black;
        font-weight: bold;
        placeholder-color: rgba(0,0,0,0.3);
        text-align: center;
    }
    .page-content{
        width: 90%;
    }
    .goodButton{
        height: 200px;
        width: 80%;
        background-color: green;
        color: white;
        border-radius: 30px;
        font-weight: bold;
    }
    .badButton{
        height: 200px;
        width: 80%;
        background-color: red;
        color: white;
        border-radius: 30px;
        font-weight: bold;
    }
    // Custom styles
</style>