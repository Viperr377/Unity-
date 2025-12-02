# Unity- Array

![2025-12-02 12-54-04 - Trim](https://github.com/user-attachments/assets/0db371ed-061a-4e2d-b469-1cdadc8c7efb)
```
using UnityEngine;

[RequireComponent(typeof(Rigidbody))]
public class PlayerController : MonoBehaviour
{
    [Header("Movement")]
    public float speed = 5f;
    public float jumpForce = 5f;

    private Rigidbody rb;
    private int totalCollectibles;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
        rb.constraints = RigidbodyConstraints.FreezeRotation; // фиксируем вращение кубика

        totalCollectibles = GameObject.FindGameObjectsWithTag("Collectible").Length;
    }

    void Update()
    {
        MovePlayer();
        Jump();
    }

    void MovePlayer()
    {
        // Получаем ввод
        float moveX = Input.GetAxis("Horizontal");
        float moveZ = Input.GetAxis("Vertical");

        // Движение относительно камеры
        Vector3 forward = Camera.main.transform.forward;
        Vector3 right = Camera.main.transform.right;
        forward.y = 0;
        right.y = 0;
        forward.Normalize();
        right.Normalize();

        Vector3 move = forward * moveZ + right * moveX;
        Vector3 moveVelocity = move * speed;

        rb.linearVelocity = new Vector3(moveVelocity.x, rb.linearVelocity.y, moveVelocity.z);

        if (move != Vector3.zero)
        {
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(move), 0.1f);
        }
    }

    void Jump()
    {
        if (Input.GetKeyDown(KeyCode.Space) && IsGrounded())
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);

        }
    }

    bool IsGrounded()
    {
        return Physics.Raycast(transform.position, Vector3.down, 0.6f);
    }

    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Collectible"))
        {
            Destroy(other.gameObject);
            totalCollectibles--;

            if (totalCollectibles <= 0)
            {
                Debug.Log("Alle ballen zijn verzameld");
                // SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
            }
        }
    }
}
```
Code
