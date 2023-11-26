# 切割 分割 爆破 fracture



添加资源 例如

![image-20231125214959069](assets/image-20231125214959069.png)



![image-20231126134554513](assets/image-20231126134554513.png)



使用破碎特效

![image-20231126134941513](assets/image-20231126134941513.png)

![image-20231126135038581](assets/image-20231126135038581.png)



#### 对箱子进行分割

要细分不然会有bug

l（选中一圈？）+p（分割）

分割开

![image-20231126135724967](assets/image-20231126135724967.png)



![image-20231126135949951](assets/image-20231126135949951.png)

![image-20231126140327560](assets/image-20231126140327560.png)

碎了 但是是实心的

![image-20231126140303176](assets/image-20231126140303176.png)



##### 加厚度 

我们给他价加个厚度，就不会了：

![image-20231126141610145](assets/image-20231126141610145.png)![image-20231126141503718](assets/image-20231126141503718.png)



![image-20231126141912482](assets/image-20231126141912482.png)

![image-20231126141940292](assets/image-20231126141940292.png)



因为有问题 咱就换一个模型再试试hh

<img src="assets/image-20231126143302506.png" alt="image-20231126143302506" style="zoom:67%;" /><img src="assets/image-20231126143319718.png" alt="image-20231126143319718" style="zoom:67%;" />





blender语法：

点击物体并tab选中

点击小键盘的“/”进入isolate模式

<img src="assets/image-20231126143801246.png" alt="image-20231126143801246" style="zoom:50%;" />

分为两部分

![image-20231126143908409](assets/image-20231126143908409.png)



分块

![image-20231126144047952](assets/image-20231126144047952.png)

![image-20231126144151455](assets/image-20231126144151455.png)

有问题就再切一次



添加材质

![image-20231126145623350](assets/image-20231126145623350.png)



加刚体

![image-20231126150003609](assets/image-20231126150003609.png)

加collider

![image-20231126150049078](assets/image-20231126150049078.png)



此时 运行已经可以碎了 （已经可以做很多东西了hh）

![image-20231126150318169](assets/image-20231126150318169.png)

#### 爆炸代码

```C#
using System.Collections;
using System.Collections.Generic;
using Unity.Mathematics;
using UnityEngine;
using Random = UnityEngine.Random;

public class mFractureObject : MonoBehaviour
{
    public GameObject originalObject;
    public GameObject fractureObject;

    public GameObject mexplosionVFX;

    private GameObject fractureGO;

    public float minExplosionForce, maxExplosionForce;

    public float mExplosionRadius;

    private Transform vfxPlace;
    private Vector3 vfxV3;
    [Header("shrink")] 
    public float delaytime=2f;

    public float SrinkScaleRefresh = 10f;

    // Start is called before the first frame update
    void Start()
    {
        //
        // vfxPlace.position = new Vector3(originalObject.transform.position.x, originalObject.transform.position.y,
        //     originalObject.transform.position.z);
        vfxV3= new Vector3(originalObject.transform.position.x, originalObject.transform.position.y,
            originalObject.transform.position.z);
        print(originalObject.transform.position.x+" "+originalObject.transform.position.y+" "+
            originalObject.transform.position.z);
    }

    // Update is called once per frame 
    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))//当然这是可以改的
        {
            print("l");
            Explode();
        }
    }

    public void Explode()
    {
        if (originalObject != null)
        {
            originalObject.SetActive(false);
        }

        if (fractureObject != null)
        {
            fractureGO = Instantiate(fractureObject,vfxV3,quaternion.identity) as GameObject;
            foreach (Transform tPiece in fractureGO.transform)
            {
                Rigidbody rb = tPiece.GetComponent<Rigidbody>();
                if (rb != null)
                {
                    //让他们崩开
                    rb.AddExplosionForce(Random.Range(minExplosionForce,maxExplosionForce),vfxV3,mExplosionRadius);
                }
                //慢慢变小小时
                StartCoroutine(Shrink(tPiece));
            }
            Destroy(fractureGO,10f);
        }

        if (mexplosionVFX != null)
        {
            GameObject exploVFX = Instantiate(mexplosionVFX,vfxV3,Quaternion.identity) as GameObject;
            print("ll");
            Destroy(exploVFX,10f);
        }
    }

    IEnumerator Shrink(Transform tpiece)
    {
        yield return new WaitForSeconds(delaytime);
        Vector3 newScale = tpiece.localScale;

        while (newScale.x >= 0)
        {
            newScale -= new Vector3(SrinkScaleRefresh, SrinkScaleRefresh, SrinkScaleRefresh);
            tpiece.localScale = newScale;
            yield return new WaitForSeconds(0.05f);
        }

        if (newScale.x - SrinkScaleRefresh<0)
        {
            tpiece.localScale = new Vector3(0,0,0);
        }

        // yield return new WaitForSeconds(1f);
    }
}

```



![image-20231126162354341](assets/image-20231126162354341.png)

![image-20231126153425408](assets/image-20231126153425408.png)



![爆炸_物体破碎](assets/爆炸_物体破碎.gif)
