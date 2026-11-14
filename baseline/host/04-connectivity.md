# 연결 확인 시나리오

호스트 사이 도달성을 가까운 곳부터 먼 곳 순서로 확인. 명령은 [샘플](../../samples/host/04-connectivity.md)

## 확인 순서

| 순서 | 구간 | 출발 -> 대상 | 쓰는 기술 | 선행 단계 |
|---|---|---|---|---|
| 1 | 게이트웨이 | 각 호스트 -> 자기 게이트웨이 | anycast gateway, VSX active gateway, VRRP | 해당 지역 SVI, 게이트웨이 설정 |
| 2 | 다른 VLAN (DC1) | Host A -> Host C `192.168.30.101` | VXLAN L3VNI 50001 (VRF TENANT-A, symmetric IRB) | EVPN-VXLAN |
| 4 | DC1 -> DC2 | Host A -> Host E `192.168.110.101` | **통신 안 됨(정상)**. VRF TENANT-A와 PARTNER가 서로 RT를 import하지 않고 BGW가 자기 대역만 광고 | VRF 분리 |
| 5 | DC1 -> Branch | Host A -> Host F `192.168.50.101` | leaf -> border leaf -> Hub -> PE1 -> MPLS L3VPN(VRF BR1) -> PE3 -> BGW -> DSW -> ASW1 | Hub, PE-CE eBGP, MPLS 코어, Branch |
| 5-1 | DC2 -> Branch | Host E -> Host F `192.168.50.101` | CX1·2 -> PE2 -> MPLS L3VPN(VRF BR1) -> PE3 -> BGW -> DSW -> ASW1 | PE-CE eBGP, MPLS 코어, Branch |
| 6 | Branch 내부 | Host F -> Host G `192.168.60.101` | DSW의 SVI 간 라우팅 (VRRP) | Branch SVI, VRRP |

## 결과 기록

| 일시 | 순서 | 결과 | 비고 |
|---|---|---|---|

- 경로는 `traceroute -n <대상>`으로 확인
- 각 단계의 증빙(화면, 캡처)은 해당 지역의 검증 문서에 넣음