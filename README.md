# Cost_management

## 自动清理未挂载的磁盘

```
#!/bin/bash

# 登录到 Azure 帐户
az login

# 获取未挂载的磁盘列表
unattached_disks=$(az disk list --query "[?diskState=='Unattached'].{Name:name, ResourceGroup:resourceGroup}" --output json)

# 如果有未挂载的磁盘，则删除它们
if [[ -n "$unattached_disks" ]]; then
    echo "以下未挂载的磁盘将被删除："
    echo "$unattached_disks"

    # 循环删除每个磁盘
    for disk in $(echo "$unattached_disks" | jq -r '.[] | @base64'); do
        _jq() {
            echo ${disk} | base64 --decode | jq -r ${1}
        }

        disk_name=$(_jq '.Name')
        resource_group=$(_jq '.ResourceGroup')

        echo "正在删除磁盘 $disk_name"
        az disk delete --name "$disk_name" --resource-group "$resource_group" --yes --no-wait
    done
else
    echo "没有未挂载的磁盘需要清理。"
fi

```

```
# 获取未挂载的磁盘列表
unattached_disks=$(az disk list --query "[?diskState=='Unattached'].{Name:name, ResourceGroup:resourceGroup}" --output json)
```

```
# 如果有未挂载的磁盘，则删除它们
if [[ -n "$unattached_disks" ]]; then
    echo "以下未挂载的磁盘将被删除："
    echo "$unattached_disks"

    # 循环删除每个磁盘
    for disk in $(echo "$unattached_disks" | jq -r '.[] | @base64'); do
        _jq() {
            echo ${disk} | base64 --decode | jq -r ${1}
        }

        disk_name=$(_jq '.Name')
        resource_group=$(_jq '.ResourceGroup')

        echo "正在删除磁盘 $disk_name"
        az disk delete --name "$disk_name" --resource-group "$resource_group" --yes --no-wait
    done
else
    echo "没有未挂载的磁盘需要清理。"
fi
```

