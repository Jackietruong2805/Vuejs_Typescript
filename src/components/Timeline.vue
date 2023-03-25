<script setup lang="ts">
import {ref, computed} from 'vue';
import {DateTime} from 'luxon';
import {Post, today, thisWeek, thisMonth, TimelinePost} from '../posts';
import TimelineItem from './TimelineItem.vue';

type Period = "today" | "This Week" | "This Month";

const periods= ["today", "This Week", "This Month"] as const;

const selectedPeriod = ref<Period>("today");

function selectPeriod(period: Period){
    selectedPeriod.value = period;
}

const posts= computed<TimelinePost[]>(() => [
    today,
    thisWeek,
    thisMonth
].map(post => {
    return {
        ...post,
        created: DateTime.fromISO(post.created)
    }
}).filter(post => {
    if(selectedPeriod?.value === "today"){
        return post.created >= DateTime.now().minus({day: 1})
    }
    if(selectedPeriod?.value === "This Week"){
        return post.created >= DateTime.now().minus({week: 1})
    }

    return post
}));
</script>

<template>
    <nav class="is-primary panel">
        <span class="panel-tabs">
            <a v-for="period in periods" :key="period" @click="selectPeriod(period)" :class="{'is-active': period === selectedPeriod}">{{ period }}</a>
        </span>
    </nav>
    <TimelineItem v-for="post in posts" :key="post.id" :post="post"/>
</template>
