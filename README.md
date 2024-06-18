# Enemy-not-die-bug
##-----------------Here is the enemy Script--------------------------------------------------------------------------------------------------------------------------------------------------------------------------##
using System.Collections;
using System.Collections.Generic;
using Unity.Android.Types;
using Unity.Burst.CompilerServices;
using UnityEngine;
using UnityEngine.UIElements;

public class Enemy : MonoBehaviour
{
    public GameObject blood;
    public float EnemyHealth;
    public float speed = 3f;
    public Transform shotPoint;
    public GameObject bullet;
    public Transform[] points;
    Transform dir;
    public float rate = 2f;
    public float time = 0f;
    public static Enemy instance;
    public float rad = 2.5f;
    public bool seen;
    public GameObject flash;
    public Transform flashPoint;
    public Transform vision;
    private GameObject enemy;
    private void Awake()
    {
        instance = this;
        enemy = this.gameObject;
    }
    public void Start()
    {
        dir = points[0];
        EnemyHealth = 100f;
    }
    public void Update()
    {
        if (EnemyHealth <= 0f)
        {
            Death();
            player.instance.kills++;
        }
        if (Vector3.Distance(player.instance.transform.position, vision.position) <= rad)
        {
            seen = true;
        }
        if (seen && Vector3.Distance(player.instance.transform.position, transform.position) <= 20f)
        {
            if (time <= 0)
            {
                Invoke("shoot", 0.1f);
                time = rate;
            }
            else
            {
                time -= Time.deltaTime;
            }
            Vector3 pDir = player.instance.transform.position - transform.position;
            float angle = Mathf.Atan2(pDir.y, pDir.x) * Mathf.Rad2Deg - 90f;
            transform.rotation = Quaternion.Euler(0f, 0f, angle);
            transform.position = Vector3.MoveTowards(transform.position, player.instance.transform.position, speed * Time.deltaTime);

        }
        else
        {
            seen = false;
            Vector3 pDir = dir.position - transform.position;
            float angle = Mathf.Atan2(pDir.y, pDir.x) * Mathf.Rad2Deg - 90f;
            transform.rotation = Quaternion.Euler(0f, 0f, angle);
            transform.position = Vector3.MoveTowards(transform.position, dir.position, (speed - 1) * Time.deltaTime);
            if (dir == points[1] && Vector3.Distance(transform.position, dir.position) <= 0.2f)
            {

                dir = points[0];
            }
            if (dir == points[0] && Vector3.Distance(transform.position, dir.position) <= 0.2f)
            {
                dir = points[1];
            }
        }
    }
    void OnDrawGizmosSelected()
    {
        Gizmos.color = Color.red;
        Gizmos.DrawWireSphere(vision.position, rad);
    }
    public void shoot()
    {
        Instantiate(flash, flashPoint.position, flashPoint.rotation);
        Instantiate(bullet, shotPoint.position, shotPoint.rotation);
    }
    public void Death()
    {
        Destroy(enemy);
        Instantiate(blood, transform.position, Quaternion.identity);
    }
}  
