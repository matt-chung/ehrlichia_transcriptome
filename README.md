# ehrlichia_transcriptome

## Table of Contents
1. [Assess synteny between the 9 E. chaffeensis genomes and Ehrlichia sp. HF](#synteny)



### Assess synteny between the 9 E. chaffeensis genomes and Ehrlichia sp. HF (2019-10-19) <a name="synteny"></a>

```{bash, eval = F}
CIRCOS_BIN_DIR=/usr/local/packages/circos-0.69-6/bin
MUMMER_BIN_DIR=/usr/local/packages/mummer-3.23/

arkansas_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/Arkansas.fna
heartland_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/Heartland.fna
jax_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/Jax.fna
liberty_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/Liberty.fna
oregon_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/Oregon.fna
osceola_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/Osceola.fna
stvincent_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/St_Vincent.fna
wakulla_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/Wakulla.fna
westpaces_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/West_Paces.fna
hf_genome_fna=/local/aberdeen2rw/julie/Matt_dir/PECHA/reference/HF.fna

output_dir=/local/projects-t3/EBMAL/mchung_dir/PECHA/synteny
```

#### Create a combined fasta containing all Ehrlichia genomes in analysis (2019-10-19) <a name="synteny_createcombinedref"></a>

```{bash, eval = F}
cat "$arkansas_genome_fna" "$heartland_genome_fna" "$jax_genome_fna" "$liberty_genome_fna" "$oregon_genome_fna" "$osceola_genome_fna" "$stvincent_genome_fna" "$wakulla_genome_fna" "$westpaces_genome_fna" "$hf_genome_fna" > "$output_dir"/ehrlichia_combined.fna

sed -i "s/>NC_007799/>Arkansas/g" "$output_dir"/ehrlichia_combined.fna
sed -i "s/>ctg7180000000004_1_1172721/>Heartland/g" "$output_dir"/ehrlichia_combined.fna
sed -i "s/>ctg7180000000004_61_1176950/>Jacksonville/g" "$output_dir"/ehrlichia_combined.fna
sed -i "s/>ctg7180000000002_1_1176202/>Liberty/g" "$output_dir"/ehrlichia_combined.fna
sed -i "s/>ctg7180000000003_1020_885251/>Oregon/g" "$output_dir"/ehrlichia_combined.fna
sed -i "s/>ctg7180000000003_3003_1178199/>Osceola/g" "$output_dir"/ehrlichia_combined.fna
sed -i "s/>ctg7180000000002_1_1173884/>St._Vincent/g" "$output_dir"/ehrlichia_combined.fna
sed -i "s/>ctg7180000000002_5135_1179491/>Wakulla/g" "$output_dir"/ehrlichia_combined.fna
sed -i "s/>ctg7180000000002_7540_1178474/>West_Paces/g" "$output_dir"/ehrlichia_combined.fna
sed -i "s/>ctg7180000000002_1_1148904/>HF/g" "$output_dir"/ehrlichia_combined.fna
```

#### Find syntenic regions between the Ehrlichia genomes using MUMmer (2019-10-19) <a name="synteny_mummer"></a>

```{bash, eval = F}
"$MUMMER_BIN_DIR"/nucmer --maxmatch -p "$output_dir"/ehrlichia "$output_dir"/ehrlichia_combined.fna "$output_dir"/ehrlichia_combined.fna
"$MUMMER_BIN_DIR"/show-coords -bHT "$output_dir"/ehrlichia.delta > "$output_dir"/ehrlichia.coords
```

#### Create input files to make Circos plot (2019-10-19) <a name="synteny_mummer"></a>

##### config.txt

```{bash, eval = F}
mkdir "$output_dir"/circos/
vim "$output_dir"/circos/ehrlichia.config.txt
```

```{bash, eval = F}
karyotype = /local/projects-t3/EBMAL/mchung_dir/PECHA/synteny/circos/ehrlichia.karyotype.txt

chromosomes_scale = /Paired./=0.001
#chromosomes_radius = /Paired./=1.5r

# circos default stuff
<image>
file = /local/projects-t3/EBMAL/mchung_dir/PECHA/synteny/circos/ehrlichia.circos.png
background = white

# cut and paste from image.generic.conf
png   = yes
svg   = yes
radius         = 1000p
angle_offset      = -90
auto_alpha_colors = yes
auto_alpha_steps  = 5
</image>
<<include /etc/colors_fonts_patterns.conf>>
<<include /etc/housekeeping.conf>>

<ideogram>
show_label = no
label_font = light
label_radius = dims(ideogram,radius_outer) + 0.05r
label_size = 24p

<spacing>
default = 0r
</spacing>

# blueuce radius to make room for contig labels
radius = 0.6r
thickness = 30p
fill = yes

</ideogram>

# links
<links>
<link>
file = /local/projects-t3/EBMAL/mchung_dir/PECHA/synteny/circos/ehrlichia.links.txt
color = black_a5
radius = 0.98r
bezier_radius = 0.1r
ribbon = yes
</link>
</links>
```

##### karyotype.txt

```{bash, eval = F}
mkdir "$output_dir"/circos/
rm "$output_dir"/circos/ehrlichia.karyotype.txt

while read LINE
do
  contig="$(echo "$LINE" | cut -f1)"
  length="$(echo "$LINE" | cut -f2)"
  echo -e "chr - "$contig"\t"$contig"\t0\t"$length"\tblack" >> "$output_dir"/circos/ehrlichia.karyotype.txt

done < <(/home/jdhotopp/bin/residues.pl "$output_dir"/ehrlichia_combined.fna)

head "$output_dir"/circos/ehrlichia.karyotype.txt
```

```{bash, eval = F}
chr - Arkansas  Arkansas        0       1176248 black
chr - Heartland Heartland       0       1172721 black
chr - Jacksonville      Jacksonville    0       1176890 black
chr - Liberty   Liberty 0       1176202 black
chr - Oregon    Oregon  0       884232  black
chr - Osceola   Osceola 0       1175197 black
chr - St._Vincent       St._Vincent     0       1173884 black
chr - Wakulla   Wakulla 0       1174357 black
chr - West_Paces        West_Paces      0       1170935 black
chr - HF        HF      0       1148904 black
```

##### links.txt

```{bash, eval = F}
awk -F "\t" '$7 != $8 {print $7"\t"$1"\t"$2"\t"$8"\t"$3"\t"$4"\tcolor=grey"}' "$output_dir"/ehrlichia.coords > "$output_dir"/circos/ehrlichia.links.txt

```

```{bash, eval = F}
"$CIRCOS_BIN_DIR"/circos -conf "$output_dir"/circos/ehrlichia.config.txt
```
