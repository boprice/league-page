<script>
    import {round} from '$lib/utils/helper'
  	import StatsRecordsAndRankings from './StatsRecordsAndRankings.svelte';

    export let key, leagueManagerRecords, leagueTeamManagers, leagueWeekHighs, leagueWeekLows, allTimeBiggestBlowouts, allTimeClosestMatchups, mostSeasonLongPoints, leastSeasonLongPoints, transactionTotals;

    let winPercentages = [];
    let lineupIQs = [];
    let fptsHistories = [];
    let tradesData = [];
    let waiversData = [];

    let showTies = false;
    
    for(const managerID in transactionTotals.allTime) {
        tradesData.push({
            managerID,
            trades: transactionTotals.allTime[managerID].trade,
        })
        waiversData.push({
            managerID,
            waivers: transactionTotals.allTime[managerID].waiver,
        })
    }


    const setRankingsData = (lRR) => {
        winPercentages = [];
        lineupIQs = [];
        fptsHistories = [];
        tradesData = [];
        waiversData = [];
        showTies = false;

        for(const key in lRR) {
            const leagueManagerRecord = lRR[key];
            const tDenominator = (leagueManagerRecord.totalWins + leagueManagerRecord.totalTies + leagueManagerRecord.totalLosses) > 0 ? (leagueManagerRecord.totalWins + leagueManagerRecord.totalTies + leagueManagerRecord.totalLosses) : 1;
            const denominator = (leagueManagerRecord.wins + leagueManagerRecord.ties + leagueManagerRecord.losses) > 0 ? (leagueManagerRecord.wins + leagueManagerRecord.ties + leagueManagerRecord.losses) : 1;
            winPercentages.push({
                managerID: key,
                percentage: round((leagueManagerRecord.totalWins + leagueManagerRecord.totalTies / 2) / tDenominator * 100),
                wins: leagueManagerRecord.totalWins,
                ties: leagueManagerRecord.totalTies,
                losses: leagueManagerRecord.totalLosses,
            })

            let lineupIQ = {
                managerID: key,
                fpts: round(leagueManagerRecord.fptsFor),
            }

            if(leagueManagerRecord.potentialPoints) {
                lineupIQ.iq = round(leagueManagerRecord.fptsFor / leagueManagerRecord.potentialPoints * 100);
                lineupIQ.potentialPoints = round(leagueManagerRecord.potentialPoints);
                lineupIQ.potentialPointsPerGame = round(leagueManagerRecord.potentialPointsPerGame);
            }

            lineupIQs.push(lineupIQ)
        
            fptsHistories.push({
                managerID: key,
                fptsFor: round(leagueManagerRecord.fptsFor),
                fptsAgainst: round(leagueManagerRecord.fptsAgainst),
                fptsPerGame: round(leagueManagerRecord.fptsFor / denominator),
            })
        
            if(leagueManagerRecord.ties > 0) showTies = true;
        }

        for(const managerID in transactionTotals.allTime) {
            tradesData.push({
                managerID,
                trades: transactionTotals.allTime[managerID].trade,
            })
            waiversData.push({
                managerID,
                waivers: transactionTotals.allTime[managerID].waiver,
            })
        }


        winPercentages.sort((a, b) => b.percentage - a.percentage);
        lineupIQs.sort((a, b) => b.iq - a.iq);
        fptsHistories.sort((a, b) => b.fptsPerGame - a.fptsPerGame);
        tradesData.sort((a, b) => b.trades - a.trades);
        waiversData.sort((a, b) => b.waivers - a.waivers);
    }

    $:setRankingsData(leagueManagerRecords)
</script>

<StatsRecordsAndRankings
    blowouts={allTimeBiggestBlowouts}
    closestMatchups={allTimeClosestMatchups}
    weekRecords={leagueWeekHighs}
    weekLows={leagueWeekLows}
    seasonLongRecords={mostSeasonLongPoints}
    seasonLongLows={leastSeasonLongPoints}
    {showTies}
    {winPercentages}
    {fptsHistories}
    {lineupIQs}
    {tradesData}
    {waiversData}
    prefix="All-Time"
    allTime={true}
    {leagueTeamManagers}
    {key}
/>
