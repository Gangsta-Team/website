<template>
    <div class="container-fluid h-100">
        <div class="row">
            <nav class="navbar navbar-dark navbar-expand-lg bg-dark sticky-top" id="doc-nav">
                <div class="container-fluid d-flex">
                    <div class="d-flex" style="width: 250px;">
                        <button type="button" id="sidebarCollapse" class="btn btn-dark">
                            <span class="navbar-toggler-icon"></span>
                        </button>
                        <a class="navbar-brand navbar-brand d-flex justify-content-center align-items-center ms-3" href="/">
                            <img src="@/assets/logo.png" alt="Logo" height="24" class="bg-white p-1 rounded d-inline-block align-text-top me-2">
                            STWIY
                        </a>
                    </div>
                    <Breadcrumb/>
                </div>
            </nav>
            <div class="d-flex p-0" id="main-view">
                <nav id="sidebar" class=" bg-light">
                    <ul class="list-unstyled overflow-auto m-0 p-2" id="doc-list">
                        <li class="mb-1">
                            <a :class="'btn btn-toggle align-items-center rounded'+((working_group=='classes') ?'':' collapsed')" data-menu="classes" data-bs-toggle="collapse" data-bs-target="#classes-collapse" :aria-expanded="((working_group=='classes') ?'true':'false')">
                                Classes
                            </a>
                            <div :class="'collapse'+((working_group=='classes') ?' show':'')" id="classes-collapse">
                                <ul class="btn-toggle-nav list-unstyled fw-normal pb-1 small">
                                    <li v-for="(key, val) in classFunctions" v-bind:key="val">
                                        <a class="btn btn-toggle align-items-center rounded native-link class-btn" :data-href="'/website/#/documentation/game/natives?class='+val" :data-class="val" :data-md="'/classes/'+val+'/README.md'" @click="handleClassLink(val, false)" data-bs-toggle="collapse" :data-bs-target="'#'+val.toLowerCase()+'-collapse'" aria-expanded="false">
                                            {{ val }}
                                        </a>
                                        <div :class="'collapse'+((working_class==val) ?' show':'')" :id="val.toLowerCase() + '-collapse'">
                                            <ul class="btn-toggle-nav list-unstyled fw-normal pb-1 small">
                                                <li class="ms-4" v-for="(props, method_name) in key" v-bind:key="method_name">
                                                    <a 
                                                    :href="'/website/#/documentation/game/natives?addr='+props.address" 
                                                    @click="handleNativeLink(props.address, false)" 
                                                    :class="'link-dark rounded native-link class-link'+((working_method ==method_name) ? ' active':'')" 
                                                    :data-class="val" 
                                                    :data-method="method_name" 
                                                    :data-addr="props.address"
                                                    :data-md="'/classes/'+val+'/'+method_name+'.md'"
                                                    data-group="classes" >{{ method_name }}</a>
                                                </li>
                                            </ul>
                                        </div>
                                    </li>
                                </ul>
                            </div>
                        </li>
                        <li class="mb-1">
                            <button :class="'btn btn-toggle align-items-center rounded'+((working_group=='globals') ?'':' collapsed')" data-menu="globals" data-bs-toggle="collapse" data-bs-target="#globals-collapse" :aria-expanded="((working_group=='globals') ?'true':'false')">
                                Globals
                            </button>
                            <div :class="'collapse'+((working_group=='globals') ?' show':'')" id="globals-collapse" style="">
                                <ul class="btn-toggle-nav list-unstyled fw-normal pb-1 small">
                                    <li class="ms-3" v-for="(key, val) in globalFunctions" v-bind:key="val">
                                        <a 
                                            :href="'/website/#/documentation/game/natives?addr='+key.address" 
                                            @click="handleNativeLink(key.address, false)" 
                                            :class="'link-dark rounded native-link global-link'+((working_method ==val) ? ' active':'')" 
                                            :data-method="val"
                                            :data-addr="key.address"
                                            :data-md="'/globals/'+val+'.md'"
                                            data-group="globals">{{ val }}</a>
                                    </li>
                                </ul>
                            </div>
                        </li>
                    </ul>
                </nav>
                <div class="w-100 overflow-auto p-3" style="width: calc(100% - 280px)"  id="native">
                </div>
            </div>
        </div>
    </div>
</template>

<script>

import Breadcrumb from '@/components/Breadcrumb'
import Loader from '@/components/Loader'
    
export default {
    name: 'Natives',
    components: {
        Breadcrumb,
        Loader
    },
    data(){
        return {
            classFunctions: {},
            classFunctionsLen: null,
            globalFunctions: {},
            globalFunctionsLen: null,
            returnNames: null,
            github_host: 'https://raw.githubusercontent.com/',
            repository: 'Gangsta-Team/natives',
            path: '/main/docs/',
            root_element: null,
            working_path: null,
            working_group: null,
            working_class: null,
            loaded: false,
            small_res: true,
        };
    },
    methods:{
        handleNativeLink(addr, by_url){
            var element = $('*[data-addr="'+addr+'"]');            
            this.working_class = element.attr("data-class");
            this.working_method = element.attr("data-method");
            this.working_group = element.attr("data-group");
            var md = element.attr("data-md");
            this.$store.commit("setLoading", true);
            $.ajax({
                url: "https://raw.githubusercontent.com/Gangsta-Team/natives/main/docs"+md,
                data: null,
                success: function(res){
                    $('#native').html(window.marked.parse(res));
                    window.hl.highlightAll();
                    this.$store.commit("setLoading", false);
                    if(by_url)
                        $('.native-link[data-addr="'+addr+'"]')[0].scrollIntoView();
                    if(this.small_res)
                        $('#sidebar').toggleClass('active');
                }.bind(this),
                dataType: "text"
            });
        },
        handleClassLink(class_name, by_url){
            var element = $('.class-btn[data-class="'+class_name+'"]');
            console.log(element)
            var location = element.attr("data-href");
            var md = element.attr("data-md");   
            this.working_group = "classes";
            this.working_class = element.attr("data-class");
            window.history.pushState({}, null, location);
            this.$store.commit("setLoading", true);
            $.ajax({
                url: "https://raw.githubusercontent.com/Gangsta-Team/natives/main/docs"+md,
                data: null,
                success: function(res){
                    $('#native').html(window.marked.parse(res));
                    window.hl.highlightAll();
                    $('#native table').addClass("table table-striped table-hover w-auto table-bordered");
                    this.formatTableLinks();
                    this.$store.commit("setLoading", false);
                    if(by_url){
                        $('.native-link[data-class="'+this.working_class+'"]')[0].scrollIntoView();
                        if(this.small_res)
                            $('#sidebar').toggleClass('active');
                    }
                }.bind(this),
                dataType: "text"
            });
        },
        handleQuery(){
            if(typeof this.$route.query.addr !== 'undefined'){
                this.handleNativeLink(this.$route.query.addr, true);
            } 
            if(typeof this.$route.query.class !== 'undefined'){
                console.log("class: "+this.$route.query.class)
                this.handleClassLink(this.$route.query.class, true);
            }            
        },
        adjustSize(){
            console.log("adjustSize",$(window).width())
            var app_h = $("#app").outerHeight();
            var nav_h = $('#doc-nav').outerHeight();
            $('#doc-list').css("height", (app_h - nav_h)+"px");
            $('#main-view').css("height", (app_h - nav_h)+"px");

            if ($(window).width() < 768) {
                this.small_res = true;
            }else{
                this.small_res = false;
            }
        },
        formatTableLinks(){
            var links = $('#native table').find("a").toArray();
            for(var i = 0; i < links.length; i++){
                var fn_name = this.working_path + links[i].text+".md";
                links[i].href = fn_name;
                links[i].md = fn_name;
                links[i].onclick = function(evt){
                    evt.preventDefault();
                    $("a[data-md='"+evt.target.md+"']")[0].click()
                }.bind(this)
            }
        },
    },
    updated(){
        this.$nextTick(()=>{
            if($('.class-link').length == this.classFunctionsLen && $('.global-link').length == this.globalFunctionsLen && !this.loaded){
                this.handleQuery();
                this.loaded = true;
            }
        })
    },
    mounted(){
        this.$store.commit("setLoading", true);
        this.root_element = $('#native');
        $.getJSON( this.github_host+this.repository+"/main/natives.json", function(data ) {
            //this.classFunctions = data.classFunctions;
            // Classes
            this.classFunctions = {};
            for(const class_name in data.classFunctions){
                this.classFunctions[class_name] = {};
                for(const method_name in data.classFunctions[class_name]){
                    this.classFunctions[class_name][method_name] = {};
                    var itm_data = {};
                    for(const itm in data.classFunctions[class_name][method_name]){
                        itm_data[itm] = data.classFunctions[class_name][method_name][itm];
                    }
                    this.classFunctions[class_name][method_name] = itm_data;
                    this.classFunctionsLen +=1;
                }
            }
            //this.globalFunctions = data.globalFunctions;
            this.globalFunctions = {};
            for(const method_name in data.globalFunctions){
                this.globalFunctions[method_name] = {};
                var itm_data = {};
                for(const itm in data.globalFunctions[method_name]){
                    itm_data[itm] = data.globalFunctions[method_name][itm];
                }
                this.globalFunctions[method_name] = itm_data;
                this.globalFunctionsLen +=1;
            }
            this.returnNames = data.returnNames;
            this.$store.commit("setLoading", false);
        }.bind(this));
        this.adjustSize();
        $(window).resize(function(){
            this.adjustSize();
        }.bind(this));
        $('#sidebarCollapse').on('click', function () {
            console.log($('#sidebar').hasClass('ative'));
            $('#sidebar').toggleClass('active');


            if($('#sidebar').hasClass('ative')){
                console.log("has")
            }else{
                console.log("not has")
            }
        });
    }
}
</script>


<style scoped>
main {
  display: flex;
  flex-wrap: nowrap;
  height: 100vh;
  height: -webkit-fill-available;
  max-height: 100vh;
  overflow-x: auto;
  overflow-y: hidden;
}

.b-example-divider {
  flex-shrink: 0;
  width: 1.5rem;
  height: 100vh;
  background-color: rgba(0, 0, 0, .1);
  border: solid rgba(0, 0, 0, .15);
  border-width: 1px 0;
  box-shadow: inset 0 .5em 1.5em rgba(0, 0, 0, .1), inset 0 .125em .5em rgba(0, 0, 0, .15);
}

.bi {
  vertical-align: -.125em;
  pointer-events: none;
  fill: currentColor;
}

.dropdown-toggle { outline: 0; }

.nav-flush .nav-link {
  border-radius: 0;
}

.btn-toggle {
  display: inline-flex;
  align-items: center;
  padding: .25rem .5rem;
  font-weight: 600;
  color: rgba(0, 0, 0, .65);
  background-color: transparent;
  border: 0;
}
.btn-toggle:hover,
.btn-toggle:focus {
  color: rgba(0, 0, 0, .85);
  background-color: #d2f4ea;
}

.btn-toggle::before {
  width: 1.25em;
  line-height: 0;
  content: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' width='16' height='16' viewBox='0 0 16 16'%3e%3cpath fill='none' stroke='rgba%280,0,0,.5%29' stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='M5 14l6-6-6-6'/%3e%3c/svg%3e");
  transition: transform .35s ease;
  transform-origin: .5em 50%;
}

.btn-toggle[aria-expanded="true"] {
  color: rgba(0, 0, 0, .85);
}
.btn-toggle[aria-expanded="true"]::before {
  transform: rotate(90deg);
}

.btn-toggle-nav a {
  display: inline-flex;
  padding: .1875rem .5rem;
  margin-top: .125rem;
  margin-left: 1.25rem;
  text-decoration: none;
}
.btn-toggle-nav a:hover,
.btn-toggle-nav a:focus,
.btn-toggle-nav a.active {
  background-color: #d2f4ea;
}

.scrollarea {
  overflow-y: auto;
}

.fw-semibold { font-weight: 600; }
.lh-tight { line-height: 1.25; }

table, th, td {
  border: 1px solid;
}
</style>