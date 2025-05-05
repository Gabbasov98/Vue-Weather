<template>
    <div v-if="!isLoading" class="widget">
        <div class="widget__top">
            <div class="widget__img">
                <img :src="backgroundUrl" alt="">
            </div>

            <div class="widget__info">
                <div class="widget__degree">
                    {{degree}}
                    <span>°</span>
                </div>
                <div class="widget__city text15">
                    {{city}}
                </div>
            </div>
        </div>

        <div class="widget__bottom">
            <div class="widget__day text15">
                {{day}}
            </div>

            <div class="widget__props">
                <div class="widget__prop">
                    {{description}}
                </div>
                <div class="widget__prop">
                    {{degree_min}}° / {{degree_max}}°
                </div>
            </div>

            <img :src="iconUrl" alt="" class="widget__icon">
        </div>
    </div>

    <Loader v-if="isLoading"/>
</template>

<script setup>
    import { onMounted, ref, reactive } from 'vue';

    import Loader from '#/components/Loader.vue'

    const WEATHER_APP_ID = '6e0cbc6ba8ca28e2d697827e926593e3';
    const PHOTO_APP_ID = 'mSVSjyjDg2sCsta8IU-kyC8qnKiooaz2Uije2EcqhDo';

    const userPosition = reactive({
        latitude: '',
        longitude: '',
    });

    const isLoading = ref(false);
    const city = ref('');
    const day = ref('');
    const degree = ref('');
    const degree_max = ref('');
    const degree_min = ref('');
    const description = ref('');
    const iconUrl = ref('');
    const backgroundUrl = ref('');

    onMounted(() => {
        let date = new Date()
        const weekday = new Intl.DateTimeFormat('en-GB', { weekday: 'long' }).format(date)
        const dayMonth = new Intl.DateTimeFormat('en-GB', { day: 'numeric', month: 'long' }).format(date)

        day.value = `${weekday}, ${dayMonth}`

        navigator.geolocation.getCurrentPosition(
            (position) => {
                userPosition.latitude = position.coords.latitude
                userPosition.longitude = position.coords.longitude

                getCityWeather()

                getPhoto()
            },
            (error) => {
                console.error('Ошибка получения геопозиции:', error.message)
            }
        )


    })

    async function getCityWeather() {
        isLoading.value = true
        let result = await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${userPosition.latitude}&lon=${userPosition.longitude}&exclude=current&APPID=${WEATHER_APP_ID}`)
        let data =  await result.json()

        city.value = data.name
        degree.value = convertToCelsius(data.main.temp)
        degree_max.value = convertToCelsius(data.main.temp_max)
        degree_min.value = convertToCelsius(data.main.temp_min)
        description.value = data.weather[0].description
        iconUrl.value = "http://openweathermap.org/img/w/" + data.weather[0].icon + ".png"

        isLoading.value = false
    }

    async function getPhoto() {
        let result = await fetch(`https://api.unsplash.com/search/photos?page=1&query=${city} city`,{
            headers: {
                Authorization: `Client-ID ${PHOTO_APP_ID}`
            }
        })
        let data =  await result.json()

        backgroundUrl.value = data.results[0].urls.small
    }


    function convertToCelsius(value) {
        return (value - 273.15).toFixed(0)
    }
</script>

<style scoped lang="scss">
    .text15{
        font-size: 0.9375em;
        line-height: 133%;
    }

    .widget{
        width: 15.1875em;
        max-width: 100%;
        margin: 3em auto;
        border-radius: 1em;
        box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.25);
        background-color: $white;
        position: relative;
        &__img{
            @include imgCover(98.27%);
            border-radius: 1em 1em 0 0;
            &:before{
                content: '';
                position: absolute;
                top: 0;
                left: 0;
                border-radius: inherit;
                background: linear-gradient(325deg, rgba(239, 188, 56, 0.14) 14.05%, rgba(250, 186, 24, 0.20) 100%), linear-gradient(271deg, #FDD368 -4.18%, #F8B70F -4.17%, rgba(222, 194, 123, 0.60) 110.03%);
                z-index: 2;
                width: 100%;
                height: 100%;
            }
        }
        &__info{
            position: absolute;
            top: 1.5em;
            left: 1.5em;
            color: $white;
            z-index: 3;
            display: flex;
            flex-direction: column;
            align-items: flex-start;
        }
        &__degree{
            font-size: 2.5em;
            letter-spacing: -.02em;
            line-height: 115%;
            position: relative;
            span{
                position: absolute;
                left: 100%;
                top: 0;
                margin-top: -.125em;
                margin-left: .0625em;
            }
        }
        &__city{
            text-transform: uppercase;
            letter-spacing: -.066em;
        }
        &__bottom{
            padding: 1em;
            position: relative;
        }
        &__day{
            opacity: .7;
        }
        &__props{
            margin-top: .375em;
            @include grid100gap(.125em);
        }
        &__prop{
            font-size: .75em;
            line-height: 133%;
            opacity: .4;
            color: #6B6B6B;
        }
        &__icon{
            position: absolute;
            right: 1.25em;
            bottom: 1.44em;
            width: 1.875em;
        }
    }
</style>
