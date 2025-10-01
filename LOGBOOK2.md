# CVE-2025-53367

## Identificação:
DjVuLibre antes da versão 3.5.29: MMRDecoder::scanruns causa Out-of-Bounds Write por apontador xr não verificado.

Escrita além do heap e leitura por pr possíveis, levando à corrupção de memória.

## Catalogação:
Descoberta por Antonio Morales, ao fuzzing do Evince.

Publicado em 2025-07-03 21:15:27.

Gravidade ALTA — CVSS 8.4.

## Exploit:
Abrir ficheiro DjVu malicioso em visualizador que usa DjVuLibre.

Permite execução de código arbitrário; funcionamento não totalmente confiável.

Problemas com confinamento (AppArmor) reduzem fiabilidade do exploit.

## Ataques: 
PoC pública disponível no blog do GitHub Security Lab.

https://github.blog/security/vulnerability-research/cve-2025-53367-an-exploitable-out-of-bounds-write-in-djvulibre/

Potencial para comprometer visualizadores e executar código remoto.


## Correção/contramedidas:
Atualizar DjVuLibre para versão 3.5.29 ou superior.

Verificação: usar checks fornecidos no repositório GitHub SecurityLab.

Mitigação temporária: bloquear processamento DjVu ou reforçar perfis AppArmor.

Teste pós-patch: validar abertura de ficheiros DjVu com PoC conhecido.

## Fontes:
https://security.archlinux.org/CVE-2025-53367

https://www.cvedetails.com/cve/CVE-2025-53367/

https://github.com/github/securitylab/tree/main/SecurityExploits/DjVuLibre/MMRDecoder_scanruns_CVE-2025-53367 

https://github.blog/security/vulnerability-research/cve-2025-53367-an-exploitable-out-of-bounds-write-in-djvulibre/

https://en.wikipedia.org/wiki/Fuzzing
