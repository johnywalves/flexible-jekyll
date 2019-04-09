---
layout: post
title: "Arquivos de texto grandes (PowerShell)"
date: 2019-02-06 16:22:15 -0200
tags: [PowerShell, Arquivos]
img: files-1.jpg
output: html_document
---



Lidar com arquivos de texto extensos pode ser um pouco dificil ou demorado para facilitar com algumas funções em PowerShell

## Contar linhas

Substituir o `<caminho do arquivo>`


{% highlight powershell %}
$nlines = 0;
$file = '<caminho do arquivo>'
gc $file -read 1000 | % { $nlines += $_.Length };
[string]::Format("{0} has {1} lines", $file, $nlines)
{% endhighlight %}

## Separar arquivos por quantidades de linhas

Separar um arquivo grande de serem processados individualmente facilita o processo e controle do progresso  
Substituindo o `<caminho do arquivo>` e a `<quantidade de linhas>` o código gera `out_<número>.txt` até o fim do texto


{% highlight powershell %}
$i=0; Get-Content '<caminho do arquivo>' -ReadCount <quantidade de linhas> | %{$i++; $_ | Out-File out_$i.txt}
{% endhighlight %}

Diferente da nossa língua temos vários caracteres diferentes e acentos, podemos usar um encode para evitar problemas no exemplo estou usando o UTF8, mas temos as opções de **ASCII**, **BigEndianUnicode**, **OEM**, **String**, **Unicode**, **UTF7**, **UTF8**, **UTF8BOM**, **UF8NoBOM**, **UTF32** e **Unknown**


{% highlight powershell %}
$i=0; Get-Content '<caminho do arquivo>' -Encoding "UTF8" -ReadCount <quantidade de linhas> | %{$i++; $_ | Out-File -Encoding "UTF8" out_$i.csv}
{% endhighlight %}

## Adicionar cabeçalho para os arquivos

Substituindo o `<caminho do arquivo>` e a `<texto do cabeçalho>`


{% highlight powershell %}
$textfile = '<caminho do arquivo>'
$header = '<texto do cabeçalho>'
$($header; Get-Content $textfile) | Set-Content $textfile
{% endhighlight %}

Substituindo o `<caminho do arquivo>` e a `<caminho do arquivo cabeçalho>`


{% highlight powershell %}
$textfile = '<caminho do arquivo>'
$headerfile = '<caminho do arquivo cabeçalho>'
$(Get-Content $headerfile; Get-Content $textfile) | Set-Content $textfile
{% endhighlight %}

## Dividir as linhas mantendo o cabeçalho

Adicionando os controles de `$header = Get-Content $file -First 1` para capturar o cabeçalho na primeira linha e `$(If ($i -ne 1) {$header} Else {$null})` para adicionar nos arquivos além do primeiro o cabeçalho arquivo  
Substituindo o `<caminho do arquivo>` e a `<quantidade de linhas>`


{% highlight powershell %}
$file = '<caminho do arquivo>'
$qtd_lines = <quantidade de linhas>
$header = Get-Content $file -First 1
$i=0; Get-Content $file -Encoding "UTF8" -ReadCount $qtd_lines | %{$i++; $($(If ($i -ne 1) {$header} Else {$null}); $_) | Out-File -Encoding "UTF8" out_$i.csv}
{% endhighlight %}

## Referências 

[Get-Content - Microsoft](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-content)
