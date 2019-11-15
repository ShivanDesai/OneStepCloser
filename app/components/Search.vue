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
            <Label class="action-bar-title" text="Search"></Label>
        </ActionBar>

        <GridLayout rows="auto,*">
            <RadCalendar :eventSource="events" class="cal" row="0" viewMode="Day"></RadCalendar>
            <!-- <Label class="page-icon fa" text.decode="&#xf002;"></Label>
            <Label class="page-placeholder" :text="message"></Label> -->

        </GridLayout>
    </Page>
</template>

<script>
    import * as utils from "~/shared/utils";
    import SelectedPageService from "../shared/selected-page-service";
    const appSettings = require("tns-core-modules/application-settings");
    import { CalendarEvent } from 'nativescript-ui-calendar';
    import { Color } from "tns-core-modules/color";

    export default {
        data(){
            return{
                events: []
            }
        },
        mounted() {
            SelectedPageService.getInstance().updateSelectedPage("Search");
            this.getEvents();
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
            getEvents(){
                // var green = new Color('green');
                var x = JSON.parse(appSettings.getString("achievements"), "nan");
                if(x != "nan"){
                    if(x.length == this.events.length){
                        return;
                    }
                    for(var i = 0; i < x.length; i++){
                        var startDate = new Date(x[i].date);
                        var endDate = new Date(x[i].date);
                        for(var j = 0; j < x[i].goodStuff.length; j++){
                            startDate.setHours(x[i].goodStuff[j].time);
                            endDate.setHours(x[i].goodStuff[j].time + 1);
                            var e = new CalendarEvent(x[i].goodStuff[j].title, startDate, endDate, false, new Color('green'));
                            this.events.push(e);
                        }
                        for(var j = 0; j < x[i].badStuff.length; j++){
                            startDate.setHours(x[i].badStuff[j].time);
                            endDate.setHours(x[i].badStuff[j].time + 1);
                            var e = new CalendarEvent(x[i].badStuff[j].title, startDate, endDate, false, new Color('red'));
                            this.events.push(e);
                        }
                    }
                }
            }
        }
    };
</script>

<style scoped lang="scss">
    // Start custom common variables
    @import '../app-variables';
    // End custom common variables
    .cal{
        height: 100%;
        margin-top: 0;
    }
    // Custom styles
</style>
