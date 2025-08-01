<svelte:options customElement={{ tag: 'ga-single-overview', shadow: 'open' }}/>

<script lang="ts">
    import {onMount} from 'svelte';
    import {
        calculateSliderPositionWithPercentile,
        getColorBlueToRedWithPercentile,
        getColorMedianWithPercentile,
        getColorRedToBlueWithPercentile,
        getTranslation, suffix
    } from '../shared/functions/helpers';
    import {Service} from '../shared/utils/service';

    export let type: string;
    export let lang: string;
    export let service: Service = window.GaReportService;

    let reportData: any;
    let percentile = 0;
    let percentile_chinese = 0;

    let show = false;

    onMount(async () => {
        reportData = await service.getReport();

        switch (type) {
            case 'shield':
                percentile = Number(reportData.Spercentile);
                percentile_chinese = Number(reportData.Spercentile);
                break;
            case 'youth':
                percentile = Number(reportData.G2percentile);
                percentile_chinese = Number(reportData.G2percentile);
                break;
            case 'mature':
                percentile = Number(reportData.G0percentile);
                percentile_chinese = 100 - Number(reportData.G0percentile);
                break;
            case 'median':
                percentile = Number(reportData.G1percentile);
                percentile_chinese = Number(reportData.G1percentile) * 2;
                break;
            case 'lifestyle':
                percentile = Number(reportData.Bpercentile);
                percentile_chinese = 100 - Number(reportData.Bpercentile);
        }

        if (percentile === 100 || percentile === 0) {
            percentile = percentile === 100 ? 99 : 1;
        }

        if (percentile_chinese === 100 || percentile_chinese === 0) {
            percentile_chinese = percentile_chinese === 100 ? 99 : 1;
        }

        show = true;
    });
</script>

{#if show}
    <div class="main">
        <div class="label">
            {#if lang === 'chinese'}
                {getTranslation(lang, 'RANKS_YOU_BEFORE')}&nbsp;
                <span style="color: {type === 'shield' || type === 'youth' ? getColorRedToBlueWithPercentile(percentile) : type === 'median' ? getColorMedianWithPercentile(percentile) : getColorBlueToRedWithPercentile(percentile)}; height: 80%; display: flex;">
                 <b>{percentile_chinese}%</b>
                 </span>
                &nbsp;{getTranslation(lang, 'RANKS_YOU_AFTER')}
            {:else}
                {getTranslation(lang, 'RANKS_YOU')}&nbsp;
                <span style="color: {type === 'shield' || type === 'youth' ? getColorRedToBlueWithPercentile(percentile) : type === 'median' ? getColorMedianWithPercentile(percentile) : getColorBlueToRedWithPercentile(percentile)}; height: 80%; display: flex;">
                 <b>{percentile}{#if lang === 'japanese'}パーセンタイル{/if}</b>
                 </span>
                <sup style="color: {type === 'shield' || type === 'youth' ? getColorRedToBlueWithPercentile(percentile) : type === 'median' ? getColorMedianWithPercentile(percentile) : getColorBlueToRedWithPercentile(percentile)}; font-size: 0.65rem;"><b>{suffix(percentile, lang)}</b></sup>
                {#if lang === 'japanese'}
                    にランク付けされました。
                {:else if lang === 'english'}
                    &nbsp;percentile
                {/if}
            {/if}
        </div>
        <div class="graph">
            <div class="graph-container">
                {#if type === 'mature' || type === 'lifestyle'}
                    <div class="colorBoxShort" style="background-color: #015566;"></div>
                    <div class="colorBox" style="background-color: #015566;"></div>
                    <div class="colorBox" style="background-color: #13A195;"></div>
                    <div class="colorBox" style="background-color: #66CCAA;"></div>
                    <div class="colorBox" style="background-color: #66CCAA;"></div>
                    <div class="colorBox" style="background-color: #F2800D;"></div>
                    <div class="colorBox" style="background-color: #DF2120;"></div>
                    <div class="colorBoxShort" style="background-color: #DF2120;"></div>
                {:else if type === 'shield' || type === 'youth'}
                    <div class="colorBoxShort" style="background-color: #DF2120;"></div>
                    <div class="colorBox" style="background-color: #DF2120;"></div>
                    <div class="colorBox" style="background-color: #F2800D;"></div>
                    <div class="colorBox" style="background-color: #66CCAA;"></div>
                    <div class="colorBox" style="background-color: #66CCAA;"></div>
                    <div class="colorBox" style="background-color: #13A195;"></div>
                    <div class="colorBox" style="background-color: #015566;"></div>
                    <div class="colorBoxShort" style="background-color: #015566;"></div>
                {:else if type === 'median'}
                    <div class="colorBoxShort" style="background-color: #DF2120;"></div>
                    <div class="colorBox" style="background-color: #F2800D;"></div>
                    <div class="colorBox" style="background-color: #66CCAA;"></div>
                    <div class="colorBox" style="background-color: #13A195;"></div>
                    <div class="colorBox" style="background-color: #13A195;"></div>
                    <div class="colorBox" style="background-color: #66CCAA;"></div>
                    <div class="colorBox" style="background-color: #F2800D;"></div>
                    <div class="colorBoxShort" style="background-color: #DF2120;"></div>
                {/if}

                <div class="slider" style="left: {calculateSliderPositionWithPercentile(percentile)}%;">
                    <svg style="position: relative;" width="60" height="60" viewBox="0 0 40 40" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <path d="M20.0001 35.5418C19.6112 35.5418 19.2223 35.4724 18.8334 35.3335C18.4445 35.1946 18.0973 34.9863 17.7917 34.7085C15.9862 33.0418 14.389 31.4168 13.0001 29.8335C11.6112 28.2502 10.4515 26.7154 9.52091 25.2293C8.59036 23.7432 7.88203 22.3127 7.39591 20.9377C6.9098 19.5627 6.66675 18.2502 6.66675 17.0002C6.66675 12.8335 8.00703 9.51405 10.6876 7.04183C13.3681 4.56961 16.4723 3.3335 20.0001 3.3335C23.5279 3.3335 26.632 4.56961 29.3126 7.04183C31.9931 9.51405 33.3334 12.8335 33.3334 17.0002C33.3334 18.2502 33.0904 19.5627 32.6042 20.9377C32.1181 22.3127 31.4098 23.7432 30.4792 25.2293C29.5487 26.7154 28.389 28.2502 27.0001 29.8335C25.6112 31.4168 24.014 33.0418 22.2084 34.7085C21.9029 34.9863 21.5556 35.1946 21.1667 35.3335C20.7779 35.4724 20.389 35.5418 20.0001 35.5418Z"
                              fill="{type === 'shield' || type === 'youth' ? getColorRedToBlueWithPercentile(percentile) : type === 'mature' || type === 'lifestyle' ? getColorBlueToRedWithPercentile(percentile) : getColorMedianWithPercentile(percentile)}"/>
                    </svg>
                </div>

                <div class="slider-number" style="left: {calculateSliderPositionWithPercentile(percentile)}%;">
                    {percentile}
                </div>

                <div class="text-left" style="width: {type === 'median' ? '19.7' : '34.7'}%;">
                    {type === 'shield' || type === 'youth' || type === 'median' ? getTranslation(lang, 'WORSE') : getTranslation(lang, 'BETTER')}
                </div>
                <div class="text-middle" style="width: 29.5%;">
                    {type === 'median' ? getTranslation(lang, 'AROUND_AVERAGE') : getTranslation(lang, 'AVERAGE')}
                </div>
                <div class="text-right" style="width: {type === 'median' ? '19.8' : '34.7'}%;">
                    {type === 'shield' || type === 'youth' ? getTranslation(lang, 'BETTER') : getTranslation(lang, 'WORSE')}
                </div>
            </div>
        </div>
    </div>
{/if}

<style>
    .main {
        height: 100%;
        width: 100%;
        display: flex;
        flex-direction: column;
    }

    .label {
        height: 15%;
        width: 100%;
        display: flex;
        justify-content: center;
    }

    .graph {
        height: 85%;
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        border: 1px solid #09341F33;
        border-radius: 10px;
    }

    .graph-container {
        width: 88%;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: space-between;
        position: relative;
    }

    .colorBox {
        height: 7px;
        width: 14.3%;
        border-radius: 24px;
    }

    .colorBoxShort {
        height: 7px;
        width: 5%;
        border-radius: 24px;
    }

    .slider {
        display: flex;
        flex-direction: column;
        align-items: center;
        position: absolute;
        transform: translate(-50%, -50%);
        width: 24%;
        max-height: 70px;
    }

    .slider-number {
        display: flex;
        flex-direction: column;
        align-items: center;
        position: absolute;
        transform: translate(-50%, -140%);
        width: 24%;
        color: white;
        font-weight: 500;
    }

    .text-left {
        position: absolute;
        height: 15%;
        border-left: 1px solid #09341F33;
        border-right: 1px solid #09341F33;
        top: 62%;
        left: 0;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 0.8rem;
        color: #09341F80;
    }

    .text-middle {
        position: absolute;
        height: 15%;
        top: 62%;
        left: 35%;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 0.8rem;
        color: #09341F80;
    }

    .text-right {
        position: absolute;
        height: 15%;
        border-left: 1px solid #09341F33;
        border-right: 1px solid #09341F33;
        top: 62%;
        right: 0;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 0.8rem;
        color: #09341F80;
    }
</style>
