#Accordion bootstrap 3 con wordpress

```php
  
    <?php
        $args = array(
            'posts_per_page'      => -1,
            'post_type' => 'preguntas_frecuentes',
            'post_status' => array( 'publish')
        );
        
        $query = new WP_Query( $args );
        
        ?> 

        <?php if( $query->have_posts() ) : ?>
                <?php $cont=1; ?>
            <div class="panel-group" id="accordion" role="tablist" aria-multiselectable="true">
                
                <?php while ( $query->have_posts() ) : $query->the_post(); ?>
                <div class="panel panel-default">
                    <div class="panel-heading" role="tab" id="heading-<?php echo $cont ?>">
                        <a role="button" data-toggle="collapse" data-parent="#accordion" href="#collapse-<?php echo $cont ?>" aria-expanded="false" aria-controls="collapse-<?php echo $cont ?>">
                          <h4 class="panel-title">
                              <?php the_title(); ?>
                          </h4>
                        </a>
                    </div>
                    <div id="collapse-<?php echo $cont ?>" class="panel-collapse collapse" role="tabpanel" aria-labelledby="heading-<?php echo $cont ?>">
                      <div class="panel-body">
                       <?php the_content(); ?>
                      </div>
                    </div>
                    
                </div>
                <?php $cont++; ?>
                <?php endwhile; ?>
            </div>  
            <?php endif; ?>
        
        <?php wp_reset_query(); ?> 

```
