<template>
    <article class="tile weather">
        <div class="weather--current">
            <span class="weather--current-temperature">{{ current.temperature }}&deg;C</span>
            <span>
                <img :src="'http://openweathermap.org/img/w/' + current.weatherIcon + '.png'" alt=""  class="weather--current-weather">
            </span>
        </div>
        <ul class="weather--coming">
            <li class="weather--coming-item" v-for="(forecast, i) in forecasts" :key="i">
                <span class="weather--coming-time">{{ forecast.time.fromNow(true) }}</span>
                <span class="weather--coming-temperature">{{ forecast.temperature }}&deg;C</span>
                <span>
                    <img :src="'http://openweathermap.org/img/w/' + forecast.weatherIcon + '.png'" alt="" class="weather--coming-weather">
                </span>
            </li>
        </ul>
    </article>
</template>

<script>
    import moment from 'moment'
    import { sprintf } from 'sprintf-js'
    import { OWM_APPID } from '../constants'
    import { encodeParams } from '../utils'

    export default {
        props: {
            lat: {
                type: Number,
                required: true
            },
            lng: {
                type: Number,
                required: true
            }
        },

        data() {
            return {
                current: {
                    temperature: 0,
                    weatherIcon: ''
                },
                forecasts: [
                    {
                        temperature: 0,
                        time: moment(),
                        weatherIcon: ''
                    },
                    {
                        temperature: 0,
                        time: moment(),
                        weatherIcon: ''
                    },
                    {
                        temperature: 0,
                        time: moment(),
                        weatherIcon: ''
                    },
                    {
                        temperature: 0,
                        time: moment(),
                        weatherIcon: ''
                    },
                ]
            }
        },

        mounted() {
            this.updateWeather()

            setInterval(() => {
                this.updateWeather()
            }, 1000 * 60 * 15) // Every 5 minutes
        },
        
        methods: {
            updateWeather() {
                const url = encodeParams`http://api.openweathermap.org/data/2.5/%s?lat=${this.lat}&lon=${this.lng}&APPID=${OWM_APPID}&units=metric`

                // Current weather
                fetch(sprintf(url, 'weather')).then((response) => {
                    response.json().then((data) => {
                        this.current = {
                            temperature: data.main.temp,
                            weatherIcon: data.weather[0].icon
                        }
                    })
                })

                // Forecast weather
                fetch(sprintf(url, 'forecast')).then((response) => {
                    response.json().then((data) => {
                        const forecastsIndexes = [1, 2, 7, 15]

                        for ([i, forecast] of this.forecasts.entries()) {
                            const owmForecast = data.list[forecastsIndexes[i]]

                            forecast.time = moment(owmForecast.dt, 'X').locale('pl')
                            forecast.temperature = owmForecast.main.temp.toFixed(0)
                            forecast.weatherIcon = owmForecast.weather[0].icon
                        }
                    })
                })
            }
        }
    }
</script>

<style lang="scss" scoped>
    @import '../variables';

    .weather {
        display: flex;
        flex-direction: row;

        &--current {
            flex: 0 50%;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }

        &--current-temperature {
            font-size: 4vw;
            font-weight: 300;
        }

        &--current-weather {
            width: 90px;
        }

        &--coming {
            flex: 0 50%;
            display: flex;
            flex-direction: column;
            margin: 0;
            padding: 0 .5vw 0 1vw;
            background: $primaryTransColor;
            list-style: none;
        }

        &--coming-item {
            flex: 1;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        &--coming-time {
            margin-right: 1vw;
            font-size: 85%;
        }

        &--coming-temperature {
            margin-left: auto;
        }

        &--coming-weather {
            width: 40px;
            margin-left: .5vw;
            vertical-align: middle;
        }
    }
</style>
