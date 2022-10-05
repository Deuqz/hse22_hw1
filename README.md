# hse22_hw1

## Обязательная часть задания

#### 1. Создаю символические ссылки 
```bash
ls /usr/share/data-minor-bioinf/assembly | xargs ln -s
```

#### 2. Выбираю случайные чтения
```bash
seqtk sample -s817 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s817 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s817 oilMP_S4_L001_R1_001.fastq 1500000 > matep1.fastq
seqtk sample -s817 oilMP_S4_L001_R2_001.fastq 1500000 > matep2.fastq
```

#### 3. Оцениваю качество исходных чтений
```bash
mkdir fastqc
ls sub* matep* | xargs fastqc -o fastqc
mkdir multiqc
multiqc -o multiqc fastqc
```
Скриншоты:

![General Statistics](https://github.com/Deuqz/hse22_hw1/blob/main/pictures/before1.png)

![Quality Scores](https://github.com/Deuqz/hse22_hw1/blob/main/pictures/before2.png)

#### 4. Подрезаю чтения
```bash
platanus_trim sub1.fastq
platanus_trim sub2.fastq
platanus_internal_trim matep1.fastq
platanus_internal_trim matep2.fastq
```

#### 5. Удаляю ненужные файлы
```bash
rm sub1.fastq sum2.fastq matep1.fastq matep2.fastq
```

#### 6. Оцениваю качество подрезанных чтений
```bash
mkdir fastqc_trimmed
ls sub* matep*| xargs fastqc -o fastqc_trimmed
mkdir multiqc_trimmed
multiqc -o multiqc_trimmed fastqc_trimmed
```
Скриншоты:

![General Statistics](https://github.com/Deuqz/hse22_hw1/blob/main/pictures/after1.png)

![Quality Scores](https://github.com/Deuqz/hse22_hw1/blob/main/pictures/after2.png)

#### 7. Собираю контиги
Далее такие тяжелые команды я осознанно запускал в 4 потока, заранее проверив через *htop*, что есть вычислительные мощности.
```bash
platanus assemble -o oil -f sub1.fastq.trimmed sub2.fastq.trimmed -t 4 2> assemble.log
```

#### 8. Пишу код в гугл коллаб для анализа контигов
Код писал в гугл коллабе. Ссылка: https://colab.research.google.com/drive/1FgDHIR41G34RVR5TlKFi7fLqOwsGK6Lh?usp=sharing

Сам ноутбук также добавил в репозиторий в папку *src*.


Анализ полученных контигов:

  Количество: 632
  
  Общая длина: 3927274
  
  Наибольшая длина: 158930
  
  N50: 51588
  
#### 9. Собираю скаффолды из контигов и подрезанных чтений
```bash
platanus scaffold -o oil -c oil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed -t 4 2> scaffold.log
```

#### 10. Пишу код в гугл коллаб для анализа скаффолдов
Анализ полученных скаффолдов

  Количество: 72
  
  Общая длина: 3875407
  
  Наибольшая длина: 3834180
  
  N50: 3834180

#### 11. Считаю количество гэпов и их общую длину в самом длинном скаффолде
Количество гэпов до обрезания: 1759

Длина гэпов до обрезания: 7116

#### 12. Уменьшаю количество гэпов с помощью подрезанных чтений
```bash
platanus gap_close -o oil -c oil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed -t 4 2> gapclose.log
```

#### 13. Удаляю неиспользуемые файлы
```bash
rm sub* matep*
```

#### 14. Считаю количество гэпов и их общую длину в самом длинном скаффолде
Количество гэпов после обрезания: 578

Длина гэпов после обрезания: 2326
