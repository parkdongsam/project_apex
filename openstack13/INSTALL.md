# Red Hat OpenStack 13 설치

## Network Infomation
| Network | Subnet | VLAN ID |
|---|---|---|
| Provisioning |172.29.25.0/24||
| API |172.29.123.0/24 | 810 |
| Storage | 172.29.127.0/24 | 840 |
| Storage MGMT | 172.29.126.0/24 | 850 |
| Tenant | 172.29.122.0/24 | 860 |
| External | 172.29.130.0/24 | 870 |

## Repository
사용 가능한 subscription 목록에서 "Red Hat OpenStack Platform, Standard Support" 섹션에서  Pool ID를 확인한다.
```
#subscription-manager list --available
```
```
#subscription-manager attach --pool=[Pool ID]

Successfully attached a subscription for: Red Hat OpenStack Platform, Standard Support (4 Sockets, NFR, Partner Only)

#subscription-manager repos --disable=*
```
