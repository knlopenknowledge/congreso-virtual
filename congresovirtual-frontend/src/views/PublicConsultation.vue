<template>
    <div :class="mode==='dark'?'dark':'light'">
        <div class="container">
            <div class="row">
                <div v-if="loadPublicConsultation" class="vld-parent">
                    <img src="../assets/img/loader2.gif" style="height:600px;width:1200px" class="pl-0 ma-10 img-fluid col-12">
                </div>
                <PublicConsultationHeader v-if="!loadPublicConsultation" :publicConsultation="publicConsultation"></PublicConsultationHeader>
                <nav aria-label="breadcrumb" class="container px-0">
                    <ol class="breadcrumb" :style="mode==='dark'?'background: rgb(12, 1, 80);':''">
                        <li class="breadcrumb-item"><a href="/" :style="mode==='dark'?'color: #fff':''">{{ $t('consulta.breadcumb.inicio') }}</a></li>
                        <li class="breadcrumb-item active" aria-current="page"><a href="/public_consultations" :style="mode==='dark'?'color: #fff':''">{{ $t('consulta.breadcumb.proyectos') }}</a></li>
                        <li class="breadcrumb-item active" aria-current="page" :style="mode==='dark'?'color: #fff':''">{{ $t('consulta.breadcumb.consulta') }} {{ publicConsultation.id }}</li>
                    </ol>
                </nav>
                <div class="col-sm-12 hk-sec-wrapper hk-gallery-wrap" :style="mode==='dark'?'background: rgb(12, 1, 80);color: #fff':''">
                    <ul id="myTab" class="nav nav-light nav-tabs" role="tablist">
                        <li class="nav-item active">
                            <a @click="changeTab" id="detalle-tab" data-toggle="tab" href="#detalle" role="tab" aria-controls="detalle" aria-selected="true" class="nav-link active" :style="mode==='dark'?'color: #fff':''">
                                {{ $t('consulta.contenido.detalle') }}
                            </a>
                        </li>
                        <li class="nav-item">
                            <a @click="changeTab" id="comments-tab" data-toggle="tab" href="#comments" role="tab" aria-controls="comments" aria-selected="true" class="nav-link" :style="mode==='dark'?'color: #fff':''">
                                {{ $t('consulta.contenido.comentarios') }}
                            </a>
                        </li>
                    </ul>
                    <div class="tab-content py-25">
                        <div class="tab-pane fade show active" id="detalle" role="tabpanel" aria-labelledby="detalle-tab">
                            <div class="container">
                                <div class="row">
                                    <a class="col-12 px-5">{{ publicConsultation.resumen }}</a>
                                    <div class="col-12 px-5 my-10" v-html="publicConsultation.detalle"></div>
                                    <div v-if="publicConsultation.video" class="col-12 px-5 mt-30">
                                        <h5>{{ $t('consulta.contenido.video') }}</h5>
                                        <div class="embed-responsive embed-responsive-16by9 mt-20" v-html="publicConsultation.video"></div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="tab-pane fade show" id="comments" role="tabpanel" aria-labelledby="comments-tab">
                            <PublicConsultationComments v-if="publicConsultation.id" :publicConsultation="publicConsultation"></PublicConsultationComments>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import PublicConsultationHeader from '../components/public_consultations/PublicConsultationHeader';
    import PublicConsultationComments from './Comments';
    import axios from '../backend/axios';

    export default {
        name: 'PublicConsultation',
        components: {
            PublicConsultationComments,
            PublicConsultationHeader
        },
        props: {
            public_consultation_id: Number,
            mode: String
        },
        data() {
            return {
                publicConsultation: Object,
                loadPublicConsultation: true
            };
        },
        mounted() {
            if((this.$store.getters.modo_oscuro === 'dark') || (window.location.href.includes('dark'))) {
                this.mode = 'dark';
            } else {
                this.mode = 'light';
            }

            this.getPublicConsultation();
        },
        methods: {
            getPublicConsultation() {
                axios
                    .get('/consultations/' + this.public_consultation_id)
                    .then(res => {
                        this.publicConsultation = res.data;
                    })
                    .finally(() => {
                        this.loadPublicConsultation = false;
                    });
            },
            changeTab(e) {
                e.preventDefault();
                $(this).tab('show');
            }
        }
    };
</script>

<style>
    .arrow-steps .step {
        font-size: 14px;
        cursor: default;
        padding: 10px 10px 10px 30px;
        float: left;
        position: relative;
        background-color: #d9e3f7;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
        transition: background-color 0.2s ease;
    }

    .arrow-steps .step:after,
    .arrow-steps .step:before {
        content: " ";
        position: absolute;
        top: 0;
        right: -16px;
        width: 0;
        height: 0;
        border-top: 25px solid transparent;
        border-bottom: 25px solid transparent;
        border-left: 17px solid #9e9e9e!important;
        z-index: 2;
        transition: border-color 0.2s ease;
    }

    .arrow-steps .step:before {
        right: auto;
        left: 0;
        border-left: 17px solid #fff;
        z-index: 0;
    }

    .arrow-steps .step:first-child:before {
        border: none;
    }

    .arrow-steps .step:first-child {
        border-top-left-radius: 4px;
        border-bottom-left-radius: 4px;
    }

    .arrow-steps .step span {
        position: relative;
    }

    .arrow-steps .step span:before {
        opacity: 0;
        content: "✔";
        position: absolute;
        top: -2px;
        left: -20px;
    }

    .arrow-steps .step.done span:before {
        opacity: 1;
        -webkit-transition: opacity 0.3s ease 0.5s;
        -moz-transition: opacity 0.3s ease 0.5s;
        -ms-transition: opacity 0.3s ease 0.5s;
        transition: opacity 0.3s ease 0.5s;
    }

    .arrow-steps .step.current {
        color: #fff;
        background-color: green !important;
    }

    .arrow-steps .step.current:after {
        border-left: 17px solid  green !important;
    }
    .tab-content {
        -webkit-box-shadow: inherit !important;
        box-shadow: inherit !important;
    }
</style>