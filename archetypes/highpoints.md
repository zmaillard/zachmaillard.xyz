---
date: '{{ .Date }}'
draft: true
title: '{{ replace .File.ContentBaseName "-" " " | title }}'
category: 'highpoint'
state: State
elevation: 1000
route: 
  name: Route Name
  distance: 0.0
  elevationGain: 0
  links:
  - name: Link Display Name
    url: https://url.to/link
---