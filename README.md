# bioinf_hw1
Описание:
сборка генома бактерии, выделенной из воды с нефтью, на основе paired-end и mate-pairs чтений.
Список команд:
1. Выборка случайных чтений определенного количества.
   !seqtk sample -s2306 oil_R1.fastq 5000000 > sub1_1.fastq
   !seqtk sample -s2306 oil_R2.fastq 5000000 > sub1_2.fastq
   !seqtk sample -s2306 oilMP_S4_L001_R1_001.fastq 1500000 > subMP1_1.fastq
   !seqtk sample -s2306 oilMP_S4_L001_R2_001.fastq 1500000 > subMP1_2.fastq
2. Оценка качества исходных чтений при помощи инструментов FastQC и MultiQC.
   !/content/FastQC/fastqc sub1_1.fastq
   !/content/FastQC/fastqc sub1_2.fastq
   !/content/FastQC/fastqc subMP1_1.fastq
   !/content/FastQC/fastqc subMP1_2.fastq
   !multiqc . -o multiqc_data
4. Подрезание чтений с помощью platanus_trim и platanus_internal_trim.
   !/content/platanus_trim sub1_1.fastq sub1_2.fastq
   !/content/platanus_trim subMP1_1.fastq subMP1_2.fastq
   !/content/platanus_internal_trim sub1_1.fastq.trimmed sub1_2.fastq.trimmed
   !/content/platanus_internal_trim subMP1_1.fastq.trimmed subMP1_2.fastq.trimmed
5. Удаление ненужных файлов.
   !rm /content/hw1_bioinf_data/sub1_1.fastq /content/hw1_bioinf_data/sub1_2.fastq
   !rm /content/hw1_bioinf_data/subMP1_1.fastq /content/hw1_bioinf_data/subMP1_2.fastq
   !rm *.trimmed
6. Оценка качества подрезанных частей.
   !/content/FastQC/fastqc sub1_1.fastq.trimmed.int_trimmed
   !/content/FastQC/fastqc sub1_2.fastq.trimmed.int_trimmed
   !/content/FastQC/fastqc subMP1_1.fastq.trimmed.int_trimmed
   !/content/FastQC/fastqc subMP1_2.fastq.trimmed.int_trimmed
7. Сборка контигов.
   !/content/platanus assemble -f sub1_1.fastq.int_trimmed sub1_2.fastq.int_trimmed -m 10
8. Сборка скаффолдов.
   !/content/platanus scaffold -c out_contig.fa -IP1 sub1_1.fastq.int_trimmed sub1_2.fastq.int_trimmed -OP1 subMP1_1.fastq.int_trimmed subMP1_2.fastq.int_trimmed
9. Уменьшение кол-ва гэпов при помощи подрезанных чтений.
    !/content/platanus gap_close -c out_scaffold.fa -f sub1_1.fastq.trimmed sub1_2.fastq.trimmed
Оценка качества FastQC и MultiQC исходных чтений:
Sub1_1:
График Per base sequence content показывает, что процент в начале чтения разрознен и высок, вероятные адаптеры, которые требуют подрезания. Высокий уровень дубликатов.
  <img width="1340" height="642" alt="image" src="https://github.com/user-attachments/assets/bffaa70d-cde1-47cb-8bb7-dbd0e96ebae9" />
  <img width="1358" height="642" alt="image" src="https://github.com/user-attachments/assets/167e4392-67a5-4278-8d63-19db3cd11f52" />
  <img width="1366" height="640" alt="image" src="https://github.com/user-attachments/assets/ef842964-fd50-4ca9-8539-3ad43160471a" />

Sub1_2:
Качество чтений хорошее, однако высокий уровень дубликатов. Возможно загрязнение/дефект поверхности или реагента. Есть часто встречающаяся последовательность, вероятно отсутствие данных, но что не является критической проблемой, так как занимает маленькую долю.
  <img width="1346" height="640" alt="image" src="https://github.com/user-attachments/assets/43dad9b7-0a67-4dea-844a-846561a63bc7" />
  <img width="1358" height="648" alt="image" src="https://github.com/user-attachments/assets/da7f79f8-b41e-469d-8541-5bc4d217f2d9" />
  <img width="1360" height="648" alt="image" src="https://github.com/user-attachments/assets/2037e518-1308-443c-b470-d718092d3623" />
  <img width="1366" height="216" alt="image" src="https://github.com/user-attachments/assets/cb635178-53c9-4e1d-ae99-ef79d9d22477" />

SubMP1_1:
К концу чтений качество ухудшается, поэтому требуется подрезание. Также, на синем фоне видны локализованные яркие пятна, что говорит о возможных проблемах при секвенировании (дефект реагентов/поверхности).
  <img width="1366" height="646" alt="image" src="https://github.com/user-attachments/assets/12e60fa0-b4a4-47ff-9314-8897c58d0584" />
  <img width="1366" height="642" alt="image" src="https://github.com/user-attachments/assets/d1180ce8-e3d5-4716-8e81-03008cfc6d69" />

SubMP1_2:
Качество концевых чтений очень плохое, что видно на графике, поэтому они подлежат обязательному подрезанию. Per base sequence content указывает на возможное наличие адаптеров в начале чтения. Adapter content отчетливо показывает рост содержания адаптеров к концу чтения.
Целое количество чтений - 1 500 000, длина чтения - 251 bp. 
   <img width="1366" height="640" alt="image" src="https://github.com/user-attachments/assets/1649341f-fccc-47bd-bb63-8b248d8ba8a6" />
  <img width="1366" height="632" alt="image" src="https://github.com/user-attachments/assets/1d930221-0ff5-468f-9e87-9b730f3d9c50" />

Качество чтений неплохое, но они требуют подрезания. У mate-pairs количество дубликатов меньше (3,9%/4,5%), чем у paired-end (30,6%/32,1%). Длина чтений у paired-end значительно меньше (101 / 251). GC-состав практически идентичен (44% / 46%). 
<img width="1360" height="654" alt="image" src="https://github.com/user-attachments/assets/15de04cf-97f4-425a-815d-8d9cebe22ed1" />
<img width="1064" height="256" alt="image" src="https://github.com/user-attachments/assets/81fd2b08-5a78-48d0-86b3-3aedc8e9ec11" />

Оценка подрезанных:
Сильно изменилось число чтений у mp.Снижение количества дубликатов. Ушли проблемы (непройденные проверки) с адаптерами. Во всех случаях появилась проблема length distribution (ввиду чтений разной длины, что получилось благодаря подрезаниям). В некоторых моментах чтений стало меньше и появилась проблема Per tile sequence quality.
<img width="1366" height="638" alt="image" src="https://github.com/user-attachments/assets/7ebf4b19-a6b4-4414-a6f3-5205ac498eb5" />
<img width="1366" height="636" alt="image" src="https://github.com/user-attachments/assets/bbc147db-199d-4eac-9b82-e7773cb53dae" />
<img width="1344" height="636" alt="image" src="https://github.com/user-attachments/assets/c9506bf0-5d6d-4eb4-8f0c-caa7bf5466d2" />

На графиках отчетливо видна разница качества чтений:
<img width="1366" height="586" alt="image" src="https://github.com/user-attachments/assets/cf3ab718-ae8f-4016-a551-7e71ddc5745d" />
<img width="1366" height="558" alt="image" src="https://github.com/user-attachments/assets/f129e958-cfc5-4a7e-bb9e-5ba942727ce8" />

Наличие адаптеров:
<img width="1366" height="532" alt="image" src="https://github.com/user-attachments/assets/a4d94b87-6839-4cae-b744-c6cab9ca53db" />
<img width="1354" height="572" alt="image" src="https://github.com/user-attachments/assets/09f77724-256f-443c-82b1-fed83a8706bb" />

Ссылка на colab: https://colab.research.google.com/drive/1RbdntBXwJpdAzKPCaZCARXgFhLqxTzJJ?usp=sharing
Программы platanus, platanus_trim, platanus_internal_trim загружались в колаб локально, с ноутбука файлом. 
