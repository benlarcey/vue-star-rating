<template>
    <svg :height="getSize" :width="getSize" @mousemove="mouseMoving" @click="selected" style="overflow:visible;">

        <linearGradient :id="grad" x1="0" x2="100%" y1="0" y2="0">
            <stop :offset="getFill" :stop-color="(rtl) ? inactiveColor : activeColor" />
            <stop :offset="getFill" :stop-color="(rtl) ? activeColor : inactiveColor" />
        </linearGradient>

        <polygon :points="starPointsToString" :fill="getGradId" :stroke="borderColor" :stroke-width="borderWidth" :filter=" fill > 0 ? 'url(#filter-3)' : '' " stroke-linejoin="round" />
        <filter x="-50%" y="-50%" width="200%" height="200%" filterUnits="objectBoundingBox" id="filter-3">
            <feOffset dx="0" dy="2" in="SourceAlpha" result="shadowOffsetOuter1"></feOffset>
            <feGaussianBlur stdDeviation="1.5" in="shadowOffsetOuter1" result="shadowBlurOuter1"></feGaussianBlur>
            <feColorMatrix values="0 0 0 0 0.989530187   0 0 0 0 0.514345435   0 0 0 0 0.0230749819  0 0 0 0.5 0" type="matrix" in="shadowBlurOuter1"></feColorMatrix>
        </filter>
        <polygon :points="starPointsToString" :fill="getGradId" />

    </svg>
</template>

<script type="text/javascript">
export default {
    props: {
        fill: {
            type: Number,
            default: 0
        },
        size: {
            type: Number,
            default: 50
        },
        starId: {
            type: Number,
            required: true
        },
        activeColor: {
            type: String,
            required: true
        },
        inactiveColor: {
            type: String,
            required: true
        },
        borderColor: {
            type: String,
            default: '#000'
        },
        borderWidth: {
            type: Number,
            default: 0
        },
        padding: {
            type: Number,
            default: 0
        },
        rtl: {
            type: Boolean,
            default: false
        }
    },
    created() {
        this.calculatePoints
        this.grad = Math.random().toString(36).substring(7)
    },
    computed: {
        calculatePoints() {
            this.starPoints = this.starPoints.map((point) => {
                return ((this.size / 43) * point) + (this.borderWidth * 1.5)
            })
        },
        starPointsToString() {
            return this.starPoints.join(',')
        },
        getGradId() {
            return 'url(#' + this.grad + ')'
        },
        getSize() {
            return parseInt(this.size) + parseInt(this.borderWidth * 3) + this.padding
        },
        getFill() {
            return (this.rtl) ? 100 - this.fill + '%' : this.fill + '%'
        }
    },
    methods: {
        mouseMoving($event) {
            this.$emit('star-mouse-move', {
                event: $event,
                position: this.getPosition($event),
                id: this.starId
            })
        },
        getPosition($event) {
            // calculate position in percentage.
            var starWidth = (92 / 100) * this.size
            var position = Math.round((100 / starWidth) * $event.offsetX)
            return Math.min(position, 100)
        },
        selected($event) {
            this.$emit('star-selected', {
                id: this.starId,
                position: this.getPosition($event)
            })
        }
    },
    data() {
        return {
            starPoints: [19.8, 2.2, 6.6, 43.56, 39.6, 17.16, 0, 17.16, 33, 43.56],
            grad: ''
        }
    }
}
</script>
