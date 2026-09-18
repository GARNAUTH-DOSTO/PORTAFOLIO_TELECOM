# Lab 1: Servidor DNS con BIND9 en Ubuntu Server

## Objetivo
Configurar un servidor DNS autoritativo usando BIND9 en Ubuntu Server 22.04.

## Topología

![Topología del lab](../imagenes/lab1-topologia.png)

## Requisitos
- VM con Ubuntu Server 22.04
- 2 GB RAM, 20 GB disco

## Pasos realizados

### 1. Instalación de BIND9
```bash
sudo apt update
sudo apt install bind9 bind9utils bind9-doc -y
