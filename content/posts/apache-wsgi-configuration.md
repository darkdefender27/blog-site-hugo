+++
date = "2018-02-17T12:44:44+05:30"
title = "Deploy python wsgi apps using Gunicorn behind Apache"
draft=true

+++

**TLDR;**

In this post I have penned down my experience in configuring apache to interact with apps written in Python.

The bulk of this post is about how to set up Gunicorn as a WSGI HTTP Server to launch Python application(s) using the Flask micro-framework and Apache to act as a frontend reverse proxy.
