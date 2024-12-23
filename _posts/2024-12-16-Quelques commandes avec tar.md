---
layout: post
title:  "Quelques commandes avec tar"
categories: [ Linux ]
tags: [ tar ]
image: /assets/HSImages/images/tar0.jpg
---

`tape archive` ou encore `tar` est un outil sous Linux permettant de créer et d’extraire des archives compressées et non-compressées.

L’archivage n’est rien d’autre que la combinaison de plusieurs fichiers en un seul. Aucun gain en espace. La compression permet quant à elle de réduire la taille des fichiers en plus de les combiner.

La syntaxe de base de tar:

{% highlight ruby %} tar options nom-de-l-archive dossier-ou-fichier-à-archiver {% endhighlight %}

Depuis quelques années, tar supporte 3 différents styles de paramètres de ligne de commande:

+ Le style Unix qui est précédé par un tiret,
+ Le style GNU, précédé par deux tirets,
+ Le style BSD, qui n’est précédé par aucun tiret.

Tout au long de cet article, nous utiliserons chacun de ces styles.

Voyons quelques commandes tar utiles dans l’administration Linux.

# Archives
## Création d’une archive

{% highlight ruby %}$ tar -cvf folder.tar folder {% endhighlight %}

## Création d’une archive en gardant intact le chemin absolu des fichiers

{% highlight ruby %}$ tar cPvf folder.tar tar-test/* {% endhighlight %}

![tar1]({{ site.baseurl }}/assets/images/HSImages/tar1.png)

## Suppression d’un fichier dans une archive

{% highlight ruby %}$ tar --delete -f folder.tar file1 {% endhighlight %}

![tar2]({{ site.baseurl }}/assets/images/HSImages/tar2.png)

## Ajout d’un fichier dans une archive

{% highlight ruby %}$ tar -rvf folder.tar file6 {% endhighlight %}

## Mise à jour d’une archive

tar ne prend pas en charge la mise à jour “sur place” des fichiers. Il est cependant possible d’ajouter le fichier mis à jour à la fin d’une archive, même s’il a le même chemin que le fichier déjà présent dans l’archive. Les deux copies du fichier seront dans l’archive et le fichier ajouté ultérieurement remplacera le précédent.

![tar3]({{ site.baseurl }}/assets/images/HSImages/tar3.png)

{% highlight ruby %}$ tar uvf folder.tar file1 {% endhighlight %}

# Archives compressées

Plusieurs utilités de compressions sont supportées par tar. Les 3 plus populaires:
+ gzip représenté par l’option -z ou –gzip,
+ bzip2 représenté par l’option -j ou –bzip2,
+ xz représenté par l’option -J ou –xz.

Ces utilités de compression sont reconnaissables par les extensionns suivantes:

+ Pour gzip, on a: tar.gz ou tgz,
+ Pour bzip2, on a : tar.bz2 ou tbz2,
+ Pour xz, on a : tar.xz ou txz.

## Création d’une archive compressée

{% highlight ruby %}$ tar czvf folder.tgz folder {% endhighlight %}

## Création d’une archive compressée en gardant intact le chemin absolu des fichiers

{% highlight ruby %}$ tar cJPvf folder.xz folder {% endhighlight %}

L’ajout, la suppression, la mise à jour de fichier dans une archive compressée ne sont pas supportés par tar.
Chacune de ces opérations se réalise sous tar en trois étapes: l’extraction de l’archive compressée à l’aide de l’utilité de décompression correspondant, ensuite la réalisation de l’opération voulue et finalement la “re-compression” de l’archive.
C’est ce que nous verrons dans les lignes suivantes:

## Suppression d’un fichier dans une archive compressée

{% highlight ruby %}
$ bunzip2 folder.tar.bz2
$ tar --delete -f folder.tar.bz2 file1
$ tar cjvf folder.tar.bz2 folder.tar ou bzip2 folder.tar
{% endhighlight %}

![tar4]({{ site.baseurl }}/assets/images/HSImages/tar4.png)

## Ajout d’un fichier dans une archive compressée

{% highlight ruby %}
$ unxz folder.tar.xz
$ tar rvf folder.tar file7
$ tar cJvf folder.tar.xz folder.tar ou xz folder.tar
{% endhighlight %}

## Mise à jour d’une archive compressée

{% highlight ruby %}
$ gunzip archive.tar.gz
$ tar uvf folder.tar file2
$ tar czvf folder.tar.gz folder.tar ou gzip folder.tar
{% endhighlight %}

# Commandes communes
## Lecture

{% highlight ruby %}$ tar -tf folder.tar{% endhighlight %}

## Extraction

{% highlight ruby %}$ tar xvf folder.tar{% endhighlight %}

## Extraction d’un fichier

{% highlight ruby %}$ tar -xvf folder.tar.bz2 file2{% endhighlight %}

![tar5]({{ site.baseurl }}/assets/images/HSImages/tar5.png)

## Exclusion d’un fichier lors de l’archivage
Adapter la commande selon l’utilité de compression ou le type d’archive

{% highlight ruby %}$ tar -cjvf folder.tar.bz2 --exclude={“file5”,”file6”} file*{% endhighlight %}

## Exclusion d’un dossier ou fichier lors de l’extraction
Adapter la commande selon l’utilité de compression ou le type d’archive

{% highlight ruby %}$ tar xvf folder.tar.bz2 folder --exclude={file2,file9}{% endhighlight %}

