<template>
    <div class="wrapper">
        <!-- Sidebar  -->

        <!-- Page Content  -->
        <div id="content">
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

            <div class="d-flex" id="main-view" >
                <nav id="sidebar" class=" bg-light">
                    <ul class="list-unstyled overflow-auto m-0 p-2" id="doc-list">
                        <li class="mb-1 d-flex flex-column">
                            <button class="btn btn-toggle align-items-center rounded collapsed" @click="$router.push('/documentation/game');" data-bs-toggle="collapse" data-bs-target="#orders-collapse" :aria-expanded="(['Game', 'Characters', 'Vehicles', 'Helicopters', 'Boats', 'Planes', 'Natives'].includes($route.name)) ? true : false">
                                Game
                            </button>
                            <div :class="(['Game', 'Characters', 'Vehicles', 'Helicopters', 'Boats', 'Planes', 'Natives'].includes($route.name)) ? 'collapse show' :'collapse'" id="orders-collapse">
                                <ul class="btn-toggle-nav list-unstyled fw-normal pb-1 small">
                                    <li><router-link :to="{ name: 'Characters'}" :class="'link-dark rounded' + ((isLinkActive('Characters')) ? ' active':'')">Characters</router-link></li>
                                    <li><router-link :to="{ name: 'Vehicles'}" :class="'link-dark rounded' + ((isLinkActive('Vehicles')) ? ' active':'')">Vehicles</router-link></li>
                                    <li><router-link :to="{ name: 'Helicopters'}" :class="'link-dark rounded' + ((isLinkActive('Helicopters')) ? ' active':'')" href="/documentation/game/helicopters">Helicopters</router-link></li>
                                    <li><router-link :to="{ name: 'Boats'}" :class="'link-dark rounded' + ((isLinkActive('Boats')) ? ' active':'')" href="/documentation/game/boats">Boats</router-link></li>
                                    <li><router-link :to="{ name: 'Planes'}" :class="'link-dark rounded' + ((isLinkActive('Planes')) ? ' active':'')" href="/documentation/game/planes">Planes</router-link></li>
                                    <li><router-link :to="{ name: 'Natives'}" :class="'link-dark rounded' + ((isLinkActive('Natives')) ? ' active':'')" href="/documentation/game/natives">Natives</router-link></li>
                                </ul>
                            </div>
                        </li>
                    </ul>
                </nav>
                <main class="w-100">
                    <router-view v-if="!['Documentation'].includes($route.name)" class="overflow-auto"/>
                    <div v-else class="container-fluid">
                        <div class="row mt-3">
                            <div class="col"> 
                                <div class="p-3 mb-4 bg-light rounded-3">
                                    <div class="container-fluid py-3">
                                        <h3 class="fw-bold">Scarface The World is Yours: Documentation</h3>
                                        <hr>
                                        <p>Welcome to the <b>Scarface The World Is Yours</b> documentation, here you will find the needed information that you are searching for.</p>
                                        <p> You may find some missing information from pages since the status of the documentation is still a <b>work in progress</b>. If you need any help, feel free to use the <a target="_blank" href="https://discord.com/invite/TA9zKUXQAc">discord server</a>.</p>
                                    </div>
                                </div>
                            </div> 
                        </div>
                    </div>
                    <Loader v-if="$store.state.loading"/>
                </main>
            </div>
        </div>
    </div>
</template>

<script>

    import Breadcrumb from '@/components/Breadcrumb'
    import Loader from '@/components/Loader'

    export default {
        name: 'Documentation',
        components: {
            Breadcrumb,
            Loader
        },
        mounted(){
            this.adjustSize();
            $(window).resize(function(){
                this.adjustSize();
            }.bind(this));
            
            $('#sidebarCollapse').on('click', function () {
                $('#sidebar').toggleClass('active');
            });
        },
        methods:{
            adjustSize(){
                var app_h = $("#app").outerHeight();
                var nav_h = $('#doc-nav').outerHeight();
                $('#doc-list').css("height", (app_h - nav_h)+"px");
                $('#main-view').css("height", (app_h - nav_h)+"px");
            },
            isLinkActive($name){
                if(this.$route.name == $name) 
                    return true;
                return false;
            }
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


</style>