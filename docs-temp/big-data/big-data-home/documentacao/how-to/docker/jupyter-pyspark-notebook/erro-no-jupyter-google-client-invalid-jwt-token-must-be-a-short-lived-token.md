
[Documentação](../../../../documentacao.md) > [How-to](../../../how-to.md) > [Docker](../../docker.md) > [jupyter-pyspark-notebook](../jupyter-pyspark-notebook.md)

# Erro no Jupyter - Google Client Invalid JWT Token must be a short-lived token

## **Acessar o cmd:**

| **Acessar como usuário Administrador do docker:**                  |
|:-------------------------------------------------------------------|
| *C:\Users\(Usuario)>*docker exec -it -u root (Nome do docker) bash |

*(base) root@d1c2fb3f74b0:~#*

| **Verificar a data sistema**       |
|:-----------------------------------|
| *(base) root@d1c2fb3f74b0:~#* date |

*Tue 18 May 2021 01:57:10 PM UTC*

| **Configurar para data e hora atual**                     |
|:----------------------------------------------------------|
| *base) root@d1c2fb3f74b0:~#* sudo dpkg-reconfigure tzdata |

*debconf: unable to initialize frontend: Dialog*  
*debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/[Dialog.pm](http://Dialog.pm) line 76.)*  
*debconf: falling back to frontend: Readline*  
*Configuring tzdata*

| **Selecionar sua localização atual**   |
|:---------------------------------------|
| *Geographic area:* 2                   |

*Please select the geographic area in which you live. Subsequent configuration questions will narrow this down by*  
*presenting a list of cities, representing the time zones in which they are located.*

*1. Africa      4. Australia     7. Atlantic Ocean  10. Pacific Ocean       13. None of the above*  
*2. America     5. Arctic Ocean  8. Europe          11. System V timezones*  
*3. Antarctica  6. Asia          9. Indian Ocean    12. US*  
*Geographic area:* 2

| **Selecionar Cidade atual**   |
|:------------------------------|
| *Time zone:* 134              |

*Please select the city or region corresponding to your time zone.*

*1. Adak                       40. Costa Rica              79. Juneau                     118. Port-au-Prince*  
*2. Anchorage                  41. Creston                 80. Louisville (Kentucky)      119. Port of Spain*  
*3. Anguilla                   42. Cuiaba                  81. Monticello (Kentucky)      120. Porto Acre*  
*4. Antigua                    43. Curaçao                82. Kralendijk                 121. Porto Velho*  
*5. Araguaina                  44. Danmarkshavn            83. La Paz                     122. Puerto Rico*  
*6. Buenos Aires (Argentina)   45. Dawson                  84. Lima                       123. Punta\_Arenas*  
*7. Catamarca (Argentina)      46. Dawson Creek            85. Los Angeles                124. Rainy River*  
*8. Cordoba (Argentina)        47. Denver                  86. Lower Princes              125. Rankin Inlet*  
*9. Jujuy (Argentina)          48. Detroit                 87. Maceio                     126. Recife*  
*10. La Rioja (Argentina)      49. Dominica                88. Managua                    127. Regina*  
*11. Mendoza (Argentina)       50. Edmonton                89. Manaus                     128. Resolute*  
*12. Rio Gallegos (Argentina)  51. Eirunepe                90. Marigot                    129. Rio Branco*  
*13. Salta (Argentina)         52. El Salvador             91. Martinique                 130. Santa Isabel*  
*14. San Juan (Argentina)      53. Ensenada                92. Matamoros                  131. Santarém*  
*15. San Luis (Argentina)      54. Fort\_Nelson             93. Mazatlan                   132. Santiago*  
*16. Tucuman (Argentina)       55. Fortaleza               94. Menominee                  133. Santo Domingo*  
*17. Ushuaia (Argentina)       56. Glace Bay               95. Merida                     134. São Paulo*  
*18. Aruba                     57. Godthab                 96. Metlakatla                 135. Scoresbysund*  
*19. Asuncion                  58. Goose Bay               97. Mexico City                136. Shiprock*  
*20. Atikokan                  59. Grand Turk              98. Miquelon                   137. Sitka*  
*21. Atka                      60. Grenada                 99. Moncton                    138. St Barthelemy*  
*22. Bahia                     61. Guadeloupe              100. Monterrey                 139. St Johns*  
*23. Bahia\_Banderas            62. Guatemala               101. Montevideo                140. St Kitts*  
*24. Barbados                  63. Guayaquil               102. Montreal                  141. St Lucia*  
*25. Belem                     64. Guyana                  103. Montserrat                142. St Thomas*  
*[More] 134*

*26. Belize                    65. Halifax                 104. Nassau                    143. St Vincent*  
*27. Blanc-Sablon              66. Havana                  105. New York                  144. Swift Current*  
*28. Boa Vista                 67. Hermosillo              106. Nipigon                   145. Tegucigalpa*  
*29. Bogota                    68. Indianapolis (Indiana)  107. Nome                      146. Thule*  
*30. Boise                     69. Knox (Indiana)          108. Fernando de Noronha       147. Thunder Bay*  
*31. Cambridge Bay             70. Marengo (Indiana)       109. Beulah (North Dakota)     148. Tijuana*  
*32. Campo Grande              71. Petersburg (Indiana)    110. Center (North Dakota)     149. Toronto*  
*33. Cancun                    72. Tell City (Indiana)     111. New Salem (North Dakota)  150. Tortola*  
*34. Caracas                   73. Vevay (Indiana)         112. Nuuk                      151. Vancouver*  
*35. Cayenne                   74. Vincennes (Indiana)     113. Ojinaga                   152. Virgin*  
*36. Cayman                    75. Winamac (Indiana)       114. Panama                    153. Whitehorse*  
*37. Chicago                   76. Inuvik                  115. Pangnirtung               154. Winnipeg*  
*38. Chihuahua                 77. Iqaluit                 116. Paramaribo                155. Yakutat*  
*39. Coral Harbour             78. Jamaica                 117. Phoenix                   156. Yellowknife*  
*Time zone:* 134

**-- Data e hora atualizada:**

*Current default time zone: 'America/Sao\_Paulo'*  
*Local time is now:      Tue May 18 11:41:37 -03 2021.*  
*Universal Time is now:  Tue May 18 14:41:37 UTC 2021.*

| **Caso, ainda não esteja atualizada, inserir este comando:**                   |
|:-------------------------------------------------------------------------------|
| rm /etc/localtime; ln -s /usr/share/zoneinfo/America/Sao\_Paulo /etc/localtime |

| *Verificar a data atual*           |
|:-----------------------------------|
| *(base) root@d1c2fb3f74b0:~#* date |

*Tue 18 May 2021 11:41:40 AM -03*
