---
title: Plan a virtual network for Azure HDInsight
description: Learn how to plan an Azure Virtual Network deployment to connect HDInsight to other cloud resources, or resources in your datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: d337d026e89d2383e25498288ba11a9c60f77b39
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229000"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Plan a virtual network for Azure HDInsight

This article provides background information on using [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) with Azure HDInsight. It also discusses design and implementation decisions that must be made before you can implement a virtual network for your HDInsight cluster. Once the planning phase is finished, you can proceed to [Create virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md). For more information on HDInsight management IP addresses that are needed to properly configure network security groups and user-defined routes, see [HDInsight management IP addresses](hdinsight-management-ip-addresses.md).

使用 Azure 虛擬網路可啟用下列案例：

* 從內部部署網路直接連線至 HDInsight。
* 將 HDInsight 連線至 Azure 虛擬網路中的資料存放區。
* 直接存取無法透過網際網路公開使用的 [Apache Hadoop](https://hadoop.apache.org/) 服務。 例如，[Apache Kafka](https://kafka.apache.org/) API 或 [Apache HBase](https://hbase.apache.org/) Java API。

> [!IMPORTANT]
> Creating an HDInsight cluster in a VNET will create several networking resources, such as NICs and load balancers. Do **not** delete these networking resources, as they are needed for your cluster to function correctly with the VNET.
>
> After Feb 28, 2019, the networking resources (such as NICs, LBs, etc) for NEW HDInsight clusters created in a VNET will be provisioned in the same HDInsight cluster resource group. Previously, these resources were provisioned in the VNET resource group. There is no change to the current running clusters and those clusters created without a VNET.

## <a name="planning"></a>計劃

規劃在虛擬網路中安裝 HDInsight 時，您必須回答的問題如下：

* 您需要將 HDInsight 安裝到現有虛擬網路嗎？ 或者，您要建立新的網路嗎？

    如果您要使用現有虛擬網路，則可能需要先修改網路設定，才能安裝 HDInsight。 如需詳細資訊，請參閱[將 HDInsight 新增至現有虛擬網路](#existingvnet)一節。

* 您要將包含 HDInsight 的虛擬網路連線至另一個虛擬網路或內部部署網路嗎？

    若要輕鬆地跨網路使用資源，您可能需要建立自訂 DNS，並設定 DNS 轉送。 如需詳細資訊，請參閱[連線多個網路](#multinet)一節。

* 您要將輸入或輸出流量限制/重新導向至 HDInsight 嗎？

    HDInsight 必須具有 Azure 資料中心內特定 IP 位址的無限制通訊。 另外還有數個連接埠必須允許通過防火牆才能進行用戶端通訊。 如需詳細資訊，請參閱[控制網路流量](#networktraffic)一節。

## <a id="existingvnet"></a>將 HDInsight 新增至現有虛擬網路

使用本節中的步驟，以了解如何將新的 HDInsight 新增至現有 Azure 虛擬網路。

> [!NOTE]  
> 您無法在虛擬網路中新增現有的 HDInsight 叢集。

1. 您要使用虛擬網路的傳統或 Resource Manager 部署模型嗎？

    HDInsight 3.4 和更新版本需要 Resource Manager 虛擬網路。 舊版 HDInsight 需要傳統虛擬網路。

    如果您的現有網路是傳統虛擬網路，則必須建立 Resource Manager 虛擬網路，然後連線兩者。 [將傳統 VNet 連線至新的 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

    加入之後，Resource Manager 網路中所安裝的 HDInsight 就可以與傳統網路中的資源互動。

2. 您使用網路安全性群組、使用者定義路由或虛擬網路設備來限制傳入或傳出虛擬網路的流量嗎？

    HDInsight 是一個受控服務，需要 Azure 資料中心內數個 IP 位址的無限制存取權。 若要允許與這些 IP 位址的通訊，請更新任何現有網路安全性群組或使用者定義路由。
    
    HDInsight 會裝載多個使用各種連接埠的服務。 不會封鎖對這些連接埠的流量。 如需允許通過虛擬設備防火牆的連接埠清單，請參閱「安全性」一節。
    
    若要尋找現有安全性設定，請使用下列 Azure PowerShell 或 Azure CLI 命令：

    * 網路安全性群組

        Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        如需詳細資訊，請參閱[為網路安全性群組疑難排解](../virtual-network/diagnose-network-traffic-filter-problem.md)文件。

        > [!IMPORTANT]  
        > 會根據規則優先順序依序套用網路安全性群組規則。 會套用第一個符合流量模式的規則，而且未針對該流量套用其他規則。 排序從最寬鬆到最嚴格權限的規則。 如需詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../virtual-network/security-overview.md)文件。

    * 使用者定義路由

        Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        如需詳細資訊，請參閱[為路由疑難排解](../virtual-network/diagnose-network-routing-problem.md)文件。

3. 建立 HDInsight 叢集，並在設定期間選擇 Azure 虛擬網路。 使用下列文件中的步驟，以了解叢集建立程序：

    * [使用 Azure 入口網站建立 HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [使用 Azure PowerShell 建立 HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [使用 Azure 傳統 CLI 建立 HDInsight](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [使用 Azure Resource Manager 範本建立 HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > 將 HDInsight 新增至虛擬網路是選擇性的設定步驟。 請務必在設定叢集時選取虛擬網路。

## <a id="multinet"></a>連線多個網路

多網路設定的最大挑戰是網路之間的名稱解析。

Azure 會針對安裝於虛擬網路中的 Azure 服務提供名稱解析。 這個內建名稱解析允許 HDInsight 使用完整網域名稱 (FQDN) 連線至下列資源：

* 網際網路上的任何可用資源。 例如，microsoft.com、windowsupdate.com。

* 相同 Azure 虛擬網路中的任何資源，方法是使用資源的「內部 DNS 名稱」。 For example, when using the default name resolution, the following are examples of internal DNS names assigned to HDInsight worker nodes:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    這些節點可以使用內部 DNS 名稱彼此直接通訊，以及與 HDInsight 中的其他節點通訊。

預設名稱解析「不」允許 HDInsight 解析加入虛擬網路之網路中的資源名稱。 例如，通常會將內部部署網路加入虛擬網路。 只使用預設名稱解析，HDInsight 無法透過名稱存取內部部署網路中的資源。 反之亦然，內部部署網路中的資源無法透過名稱存取虛擬網路中的資源。

> [!WARNING]  
> 您必須建立自訂 DNS 伺服器，並設定虛擬網路使用它，再建立 HDInsight 叢集。

若要啟用虛擬網路與所加入網路中資源之間的名稱解析，您必須執行下列動作：

1. 在要安裝 HDInsight 的 Azure 虛擬網路中建立自訂 DNS 伺服器。

2. 將虛擬網路設定為使用自訂 DNS 伺服器。

3. 尋找虛擬網路的 Azure 指派 DNS 尾碼。 此值與 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 類似。 如需尋找 DNS 尾碼的資訊，請參閱[範例：自訂 DNS](hdinsight-create-virtual-network.md#example-dns) 一節。

4. 設定 DNS 伺服器之間的轉送。 設定取決於遠端網路類型。

   * 如果遠端網路是內部部署網路，請設定 DNS，如下所示：
        
     * 「自訂 DNS」(在虛擬網路中)：

         * 將虛擬網路 DNS 尾碼的要求轉送至 Azure 遞迴解析程式 (168.63.129.16)。 Azure 會處理虛擬網路中資源的要求

         * 將所有其他要求轉送至內部部署 DNS 伺服器。 內部部署 DNS 會處理所有其他名稱解析要求，即使是網際網路資源 (例如 Microsoft.com) 的要求也是一樣。

     * 內部部署 DNS：將虛擬網路 DNS 尾碼的要求轉送至自訂 DNS 伺服器。 自訂 DNS 伺服器接著會轉送至 Azure 遞迴解析程式。

       此設定會將完整網域名稱包含虛擬網路 DNS 尾碼的要求路由傳送至自訂 DNS 伺服器。 內部部署 DNS 伺服器會處理所有其他要求 (即使是針對公用網際網路位址)。

   * 如果遠端網路是另一個 Azure 虛擬網路，請設定 DNS，如下所示：

     * 「自訂 DNS」(在每個虛擬網路中)：

         * 將虛擬網路 DNS 尾碼的要求轉送至自訂 DNS 伺服器。 每個虛擬網路中的 DNS 會負責解析其網路內的資源。

         * 將所有其他要求轉送至 Azure 遞迴解析程式。 遞迴解析程式負責解析本機和網際網路資源。

       根據 DNS 尾碼，每個網路的 DNS 伺服器都會將要求轉送至另一個。 其他要求是使用 Azure 遞迴解析程式進行解析。

     如需每個設定的範例，請參閱[範例：自訂 DNS](hdinsight-create-virtual-network.md#example-dns) 一節。

如需詳細資訊，請參閱 [VM 和角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)文件。

## <a name="directly-connect-to-apache-hadoop-services"></a>直接連線至 Apache Hadoop 服務

您可以連線至位於 `https://CLUSTERNAME.azurehdinsight.net` 的叢集。 此位址會使用公用 IP，如果您已使用 NSG 限制來自網際網路的傳入流量，則可能無法觸達該 IP。 此外，當您在 VNet 中部署叢集時，可以使用私人端點 `https://CLUSTERNAME-int.azurehdinsight.net` 來存取該叢集。 此端點會解析至 VNet 內的私人 IP，以便存取叢集。

若要透過虛擬網路連線至 Apache Ambari 和其他網頁，請使用下列步驟：

1. 若要探索 HDInsight 叢集節點的內部完整網域名稱 (FQDN)，請使用下列其中一種方法：

    Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    在所傳回的節點清單中，尋找前端節點的 FQDN 並使用這些 FQDN 來連線至 Ambari 和其他 Web 服務。 例如，使用 `http://<headnode-fqdn>:8080` 存取 Ambari。

    > [!IMPORTANT]  
    > 前端節點上裝載的某些服務一次只會在一個節點上作用。 如果您嘗試在一個前端節點上存取服務，但傳回 404 錯誤，請切換至其他的前端節點。

2. 若要判斷可提供服務的節點和連接埠，請參閱 [Hadoop 服務在 HDInsight 上所使用的連接埠](./hdinsight-hadoop-port-settings-for-services.md)文件。

## <a id="networktraffic"></a> 控制網路流量

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Techniques for controlling inbound and outbound traffic to HDInsight clusters

Azure 虛擬網路中的網路流量可以使用下列方法進行控制：

* **網路安全性群組** (NSG) 可讓您篩選輸入和輸出網路流量。 如需詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../virtual-network/security-overview.md)文件。

* **Network virtual appliances** (NVA) can be used with outbound traffic only. NVAs replicate the functionality of devices such as firewalls and routers. 如需詳細資訊，請參閱[網路設備](https://azure.microsoft.com/solutions/network-appliances)文件。

As a managed service, HDInsight requires unrestricted access to the HDInsight health and management services both for incoming and outgoing traffic from the VNET. When using NSGs, you must ensure that these services can still communicate with HDInsight cluster.

![Diagram of HDInsight entities created in Azure custom VNET](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight with network security groups

If you plan on using **network security groups** to control network traffic, perform the following actions before installing HDInsight:

1. 識別您要用於 HDInsight 的 Azure 區域。

2. Identify the service tags required by HDInsight for your region. For more information, see [Network security group (NSG) service tags for Azure HDInsight](hdinsight-service-tags.md).

3. Create or modify the network security groups for the subnet that you plan to install HDInsight into.

    * __網路安全性群組__：允許連接埠 __443__ 上來自 IP 位址的「輸入」流量。 This will ensure that HDInsight management services can reach the cluster from outside the virtual network.

For more information on network security groups, see the [overview of network security groups](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controlling outbound traffic from HDInsight clusters

For more information on controlling outbound traffic from HDInsight clusters, see [Configure outbound network traffic restriction for Azure HDInsight clusters](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premises"></a>Forced tunneling to on-premises

強制通道是一種使用者定義路由設定，其中來自子網路的所有流量都會強制流向特定網路或位置，例如內部部署網路。 HDInsight does __not__ support forced tunneling of traffic to on-premises networks. 

## <a id="hdinsight-ip"></a> 所需的 IP 位址

If you use network security groups or user-defined routes to control traffic, please see [HDInsight management IP addresses](hdinsight-management-ip-addresses.md).
    
## <a id="hdinsight-ports"></a> 所需連接埠

如果您打算使用**防火牆**並從從外部透過特定連接埠存取叢集，則可能需要在您案例所需的連接埠上允許流量。 根據預設，只要允許上一節中所述的 Azure 管理流量透過連接埠 443 到達叢集，就不需要設定任何特殊的連接埠允許清單。

如需特定服務的連接埠清單，請參閱 [HDInsight 上 Apache Hadoop 服務所使用的連接埠](hdinsight-hadoop-port-settings-for-services.md)文件。

如需虛擬設備防火牆規則的詳細資訊，請參閱[虛擬設備案例](../virtual-network/virtual-network-scenario-udr-gw-nva.md)文件。

## <a name="load-balancing"></a>負載平衡

When you create an HDInsight cluster, a load balancer is created as well. The type of this load balancer is at the [basic SKU level](../load-balancer/load-balancer-overview.md#skus) which has certain constraints. One of these constraints is that if you have two virtual networks in different regions, you cannot connect to basic load balancers. See [virtual networks FAQ: constraints on global vnet peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers), for more information.

## <a name="next-steps"></a>後續步驟

* For code samples and examples of creating Azure Virtual Networks, see [Create virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md).
* 如需設定 HDInsight 連線至內部部署網路的端對端範例，請參閱[將 HDInsight 連線至內部部署網路](./connect-on-premises-network.md)。
* For configuring Apache HBase clusters in Azure virtual networks, see [Create Apache HBase clusters on HDInsight in Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* 如需設定 Apache HBase 異地複寫，請參閱[設定 Azure 虛擬網路中的 Apache HBase 叢集複寫](hbase/apache-hbase-replication.md)。
* 如需有關 Azure 虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。
* 如需網路安全性群組的詳細資訊，請參閱[網路安全性群組](../virtual-network/security-overview.md)。
* 如需使用者定義路由的詳細資訊，請參閱[使用者定義路由和 IP 轉送](../virtual-network/virtual-networks-udr-overview.md)。
