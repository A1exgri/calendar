<template>
    <div id="app" class="container calendar mt-5 ">
        <div class="card main-card">
            <div class="d-flex align-items-center">
                <a href="#" class="arrow arrow-prev" @click="prevMonth">
                    &#5176;
                </a>
                <h2 class="month">{{ monthTitle }}</h2>
                <a href="#" class="arrow arrow-next" @click="nextMonth">
                    &#5171;</a>
            </div>
            <div class="calendar__grid">
                <div class="calendar__header"
                     v-for="(day, index) in daysInWeek"
                     :key="index"
                >
                    {{ day }}
                </div>
            </div>
            <div class="calendar__grid">
                <div v-for="(day, index) in days"
                     :key="index"
                     :style="{ gridColumn: column(index) }"
                     :class="[ today(day) ? 'today' : '', earlyToday(day) ? 'gray' : '' ]"
                     class="calendar__grid-item"
                >
                    {{ day.format('D') }}
                    <div
                        v-for="(action, index) in actions"
                        :key="index"
                    >
                        <template v-if="isTime(day, action.date)">
                            <div
                                v-for="(active, ind) in actions[index].activities"
                                :key="ind"
                                 class="active-message"
                                 :class="active.color"
                                 :style="{top: `${ind * 30 + 30}px`}"
                            >
                                {{ active.time }} {{ active.title }}
                            </div>
                        </template>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import moment from "moment";

moment.locale('ru');
export default {
    name: 'App',
    components: {},
    data() {
        return {
            month: null,
            year: null,
            ind: '',
            daysInWeek: [
                'понедельник',
                'вторник',
                'среда',
                'четверг',
                'пятница',
                'суббота',
                'воскресенье'
            ],
            days: [],
            actions: [
                {
                    date: '4.02.2022', activities: [
                        {time: '16:00', title: 'Presentation', color: 'red'},
                        {time: '18:00', title: 'Коммуникационный дизайн среды', color: 'green'},
                        {time: '20:00', title: 'Семинар', color: 'orange'},
                    ]
                },
                {
                    date: '8.02.2022', activities: [
                        {time: '18:00', title: 'Семинар', color: 'orange'},
                    ]
                },
                {
                    date: '11.02.2022', activities: [
                        {time: '16:00', title: 'Presentation Company', color: 'red'},
                        {time: '18:00', title: 'Коммуникационный дизайн среды', color: 'green'},
                    ]
                },
                {
                    date: '19.02.2022', activities: [
                        {time: '18:00', title: 'Семинар', color: 'orange'},
                    ]
                },
                {
                    date: '26.02.2022', activities: [
                        {time: '16:00', title: 'Presentation', color: 'red'},
                        {time: '18:00', title: 'Коммуникационный дизайн среды', color: 'green'},
                    ]
                }
            ]
        }
    },
    mounted() {
        this.month = moment().format('M')
        this.year = moment().format('YYYY')
        this.getMonthDays(this.month)
    },
    computed: {
        monthTitle() {
            return moment().set('month', +(this.month - 1)).format('MMMM')
        }
    },
    methods: {
        getMonthDays(m) {
            let month = moment(`${this.year}.${+m}.01`).startOf('month')
            this.days = [...Array(month.daysInMonth())].map((_, i) => {
                return month.clone().add(i, 'day')
            })
        },
        column(index) {
            if (index === 0) {
                if (this.days[0].day() === 0) return 7
                return this.days[0].day()
            }
        },
        today(day) {
            return moment().isSame(day, 'day')
        },
        isTime(day, date) {
            let dateItem = `${+moment(day).format('DD')}.${moment(String(this.month)).format('MM')}.${this.year}`
            return (dateItem === date)
        },
        earlyToday(day) {
            return moment().isAfter(`${this.year}-${this.month}-${+moment(day).format('D')}`, 'day');
        },
        nextMonth() {
            if (this.month === 12) {
                this.year++
                this.month = 1;
            } else this.month++
            this.getMonthDays(this.month)
        },
        prevMonth() {
            if (this.month === 1) {
                this.year--
                return this.month = 12;
            } else this.month--
            this.getMonthDays(this.month)
        }
    }

}
</script>

<style lang="scss">
@import '~bootstrap/dist/css/bootstrap.css';

body {
    background: #ccc;
    align-items: center;
    box-sizing: border-box;
    display: flex;
    font: 16px "Helvetica", sans-serif;
    justify-content: center;
    padding: 2em;
}

.calendar {
    .main-card {
        border-radius: 50px;
        padding: 20px;
    }

    .month {
        color: darkblue;
        text-transform: uppercase;
        padding: 0 20px;
    }

    a.arrow {
        font-size: 28px;
        text-decoration: none;
        color: #000;
        padding-bottom: 10px;
        cursor: pointer;
    }

    &__grid {
        display: grid;
        grid-template-columns: repeat(7, 1fr);
        width: 100%;
    }

    &__header {
        text-transform: uppercase;
        font-weight: bold;
        text-align: right;
        padding: 20px 0;
    }

    &__grid-item {
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: flex-end;
        padding: 10px;
        border-radius: 5px;
        border: 1px solid #cccccc55;
        height: 180px;
        position: relative;

        &.gray {
            background: #cccccc55;
            border: 1px solid #fff;
        }

        &.today {
            border: 2px solid gray;
        }
    }

    .active-message {
        border-radius: 5px;
        margin-bottom: 5px;
        padding: 5px;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        position: absolute;
        left: 5px;
        right: 5px;
        min-width: 170px;
        z-index: 10;
        cursor: pointer;

        &.red {
            color: red;
            background: lightpink;
        }

        &.green {
            color: green;
            background: lightgreen;
        }

        &.orange {
            color: orange;
            background: lightgoldenrodyellow;
        }

        &:hover {
            right: auto;

        }
    }
}
</style>
