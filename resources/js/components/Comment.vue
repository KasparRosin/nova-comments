<template>
    <div class="commenter__comment py-4 border-t border-40">
        <div class="font-light text-80 text-sm">
            <template v-if="hasCommenter">
                <a class="no-underline dim text-primary font-bold" :href="commenterUrl" v-text="commenter"></a>

                said
            </template>

            <template v-else>
                Written
            </template>

            {{ date }}
        </div>

        <div class="mt-2">
            <read-more more-str=">>>" :text="commentString" link="#" less-str="<<<" :max-chars="150"></read-more>
        </div>
    </div>
</template>

<script>
    // require('moment-timezone');
    import ReadMore from 'vue-read-more/components/ReadMoreComponent';

    export default {
        props: {
            comment: {
                type: Object,
                required: true
            }
        },

        components: { ReadMore },

        computed: {
            commentString() {
                return _.find(this.comment.fields, { attribute: 'comment' }).value;
            },

            commenter() {
                return _.find(this.comment.fields, { attribute: 'commenter' }).value;
            },

            commenterUrl() {
                let commenterId = _.find(this.comment.fields, { attribute: 'commenter' }).belongsToId;

                return `/nova/resources/users/${commenterId}`;
            },

            date() {
                let now = moment();
                let date = moment.utc(_.find(this.comment.fields, { attribute: 'created_at' }).value)
                    .tz(moment.tz.guess());

                if (date.isSame(now, 'minute')) {
                    return 'just now';
                }

                if (date.isSame(now, 'day')) {
                    return `at ${date.format('LT')}`;
                }

                if (date.isSame(now, 'year')) {
                    return `on ${date.format('MMM D')}`;
                }

                return `on ${date.format('ll')}`;
            },

            hasCommenter() {
                return Boolean(this.commenter);
            }
        }
    }
</script>
