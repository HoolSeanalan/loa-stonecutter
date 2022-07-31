<template>
    <h1>Odds</h1>
</template>

<script setup lang="ts">
defineProps<{
  e1Success: number,
  e1Fail: number,
  e2Success: number,
  e2Fail: number,
  rCrack: number,
  rFail: number,
  successChance: number
}>()

let min1: number;
let min2: number;
let maxR: number;
let stoneMax: number;

function getChances(e1Success: number, e1Fail: number, e2Success: number, e2Fail: number, rCrack: number, rFail: number, successChance: number) {
    let finalChances: number[] = [0, 0, 0];
    //if impossible to get desired stone all choices 0%
    if (e1Fail > stoneMax-min1 || e2Fail > stoneMax-min2 || rCrack > maxR) {
        return finalChances
    }
    //if impossible to get worse than desired stone all choices 100%
    else if (e1Success >= min1 && e2Success >= min2 && rFail >= stoneMax-maxR) {
        finalChances = [1, 1, 1]

        return finalChances
    }
    else {
        let successBest: number;
        let failBest: number;

        //tap engraving 1
        if (e1Success+e1Fail < stoneMax) {
            //odds if tap succeeds
            successBest = getLargest(getChances(e1Success++, e1Fail, e2Success, e2Fail, rCrack, rFail, successChance-.1))

            //odds if tap fails
            failBest = getLargest(getChances(e1Success, e1Fail++, e2Success, e2Fail, rCrack, rFail, successChance+.1))

            //save engraving 1 tap odds
            finalChances[0] = ((successBest+failBest)/2)
        }

        //tap engraving 2
        if (e2Success+e2Fail < stoneMax) {
            //odds if tap succeeds
            successBest = getLargest(getChances(e1Success, e1Fail, e2Success++, e2Fail, rCrack, rFail, successChance-.1))

            //odds if tap fails
            failBest = getLargest(getChances(e1Success, e1Fail, e2Success, e2Fail++, rCrack, rFail, successChance+.1))

            //save engraving 2 tap odds
            finalChances[1] = ((successBest+failBest)/2)
        }

        //tap reduction
        if (rCrack+rFail < stoneMax) {
            //odds if tap cracks
            successBest = getLargest(getChances(e1Success, e1Fail, e2Success, e2Fail, rCrack++, rFail, successChance-.1))

            //odds if tap fails
            failBest = getLargest(getChances(e1Success, e1Fail, e2Success, e2Fail, rCrack, rFail++, successChance+.1))

            //save reduction tap odds
            finalChances[2] = ((successBest+failBest)/2)
        }
    }

    //return odds for each tap option; 0 for untappable option
    return finalChances;
}

//get largest number in length 3 array
function getLargest(odds: number[]) {
    let largest: number

    if (odds[0] >= odds[1] && odds[0] >= odds[2]) {
        largest = odds[0]
    }
    else if (odds[1] >= odds[2]) {
        largest = odds[1]
    }
    else {
        largest = odds[2]
    }

    return largest
}
</script>

