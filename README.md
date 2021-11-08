{% extends "layout.html" %}

{% block content %}
<div class="container body-content">
    <!-- banner -->
    <div class="main-w3pvt">
        <div class="container-fluid">
            <div class="row">
                <div class="col-lg-6 style-banner">
                    <div class="style-banner-inner">
                        <h3 class="font-weight-bold text-uppercase">
                            <span class="font-weight-normal">Live</span> Upload Crop Image <span class="font-weight-normal">Live</span>
                            Identify Insect(Disease)
                        </h3>
                        <p class="mt-3">Leafs, Plants & Crop Images</p>
                        <a href="{{url_for('register')}}" class="btn button-style mt-sm-5 mt-4">Register here!!</a>
                    </div>
                </div>
                <div class="col-lg-6 img-banner-w3 text-center">
                    <div class="csslider infinity" id="slider1">
                        <input type="radio" name="slides" checked="checked" id="slides_1" />
                        <input type="radio" name="slides" id="slides_2" />
                        <input type="radio" name="slides" id="slides_3" />
                        <input type="radio" name="slides" id="slides_4" />
                        <ul class="banner_slide_bg">
                            <li>
                                <img src="/static/images/1.jpg" alt="" class="img-fluid">
                            </li>
                            <li>
                                <img src="/static/images/2.jpg" alt="" class="img-fluid">
                            </li>
                            <li>
                                <img src="/static/images/3.jpg" alt="" class="img-fluid">
                            </li>
                            <li>
                                <img src="/static/images/4.jpg" alt="" class="img-fluid">
                            </li>
                        </ul>
                        <div class="navigation">
                            <div>
                                <label for="slides_1"></label>
                                <label for="slides_2"></label>
                                <label for="slides_3"></label>
                                <label for="slides_4"></label>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <!-- //banner -->
</div>

{% endblock %}

